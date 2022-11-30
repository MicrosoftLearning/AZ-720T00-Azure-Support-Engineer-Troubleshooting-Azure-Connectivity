---
lab:
    title: 'Lab 7 - Troubleshoot routing, traffic control and load balancing issues'
    module: 'Troubleshoot routing, traffic control and load balancing in Microsoft Azure'
---

An Azure subscription is provided for this lab, see the credentials above. If you're interested in completing this lab using your own Azure subscription, sign up for a free trial at <https://azure.microsoft.com/free/>.

# Troubleshoot routing, traffic control and load balancing issues

**Estimated Time: 10 minutes**

You work as a support engineer supporting Azure infrastructure. You've been contacted by your web team about an issue with website performance. The web team has a pool of webservers behind a load balancer and public IP address.

![Screenshot of a network topology diagram showing a pool of webservers behind a load balancer and public IP address.](../media/4-network-topology-diagram.png)

The web team thinks that the internet traffic isn't being distributed equally between all the webservers.

In this lab, you'll use what you've learned to go through steps to troubleshoot the performance issues with your organizations website.

## Check that the issue still exists

1. Sign in to the [Azure portal](https://portal.azure.com) using the credentials above, or if you'd like your own Azure subscription.

1. Use this Azure CLI command in the Cloud Shell to get the public IP address of the scale set.

    ![Screenshot showing how to access the cloud shell.](../media/mod6-cloudshell.png)

1. Select **Bash**, then select **Create storage**.
1. In the cloud shell run this command:

    ```
    az network public-ip show \
    --resource-group lab7rg \
    --name webPublicIP \
    --query '[ipAddress]' \
    --output tsv
    ```

2. Copy the IP address, in a new tab in your browser, try to navigate to it.

    > [!NOTE]
    > Your IP address will be different to the one shown in the screenshot.

1. Hit refresh in your browser, or on Windows press the **F5** ten or twenty times. Notice that you are getting a response from the same webserver every single time.

    ![Screenshot showing a single web server returing a response.](../media/4-web-working.png)

1. Leave this tab open to check you resolve the issue in the next exercise.

## Check all the virtual machines are responding on port 80

1. Navigate to the [Azure portal](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true) in another browser tab.

1. Select **Virtual machines**.

    ![Screenshot of the virtual machines.](../media/4-portal-select-virtual-machines.png)

1. Select **webVirtualMachine1**.

1. Under **Settings**, select **Networking**.

    ![Screenshot of the networking settings for the virtual machine.](../media/4-virtual-machine-network-settings.png)
  
1. Check that inbound traffic is allowed on port **80**.

1. Repeat the above for **webVirtualMachine2**.

The current settings appear to be correct.

## Check the Load Balancer settings

1. Select **Home**.

1. Search for **Load balancers**.

    ![A screenshot of the Azure portal showing Load balancers being selected.](../media/4-load-balancers.png)

1. Under **Services**, select **Load balancers**.

    ![A screenshot showing the load balancers.](../media/4-list-of-load-balancers.png)

1. Select **webLoadBalancer**.

1. Under **Settings**, Select the **Frontend IP configuration**.

    ![Screenshot of the frontend IP settings of the load balancer.](../media/4-load-balancer-frontend.png)

    > [!NOTE]
    > There is a correctly configured frontend IP address. Check that it matches the IP address you have in your other tab.

1. Under **Settings**, select **Backend pools**.

    ![Screenshot of the load balancer backend pool.](../media/4-load-balancer-backend-pools.png)

    > [!NOTE]
    > The virtual machines are both running.

1. Under **Settings**, select **Load balancing rules**, then select **webLoadBalancerRule**.

    ![Screenshot showing the webLoadBalancerRule to select.](../media/4-load-balancer-rules.png)

1. Check all the settings for the load balancer rule.

    ![Screenshot of the webLoadBalancerRule settings.](../media/4-web-load-balancer-rule-settings.png)

You think you have identified the issue. At the moment once a user visits the website they are routed to one virtual machine. This will persist because of the **Session persistence** setting.

## Resolve load balancer issue

1. To resolve the backend issue, change the **Session persistence** from **Client IP and protocol** to **None**, and then select **Save**.

    ![Screenshot showing the rule fixed.](../media/5-load-balancer-fix.png)

    > [!NOTE]
    > By setting **Session persistence** to **None**, successive request from clients can be handled by different virtual machines.

1. Wait until the rule has been deployed successfully.

## Test that the issue has been resolved

Switch back to the tab where you pasted the public IP address. If you have closed the tab select the load balancer rule and the public IP address is listed there.

1. Refresh the browser 20 or 30 times, you should see the message switch between **webVirtualMachine1** and **webVirtualMachine2**.
1. The traffic is now being shared correctly between all the machines in the backend pool.

    ![Animated gif showing the different webservers responding.](../media/issue-resolved.gif)
