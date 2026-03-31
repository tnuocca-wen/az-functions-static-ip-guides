# Full Network Isolation: App Service Environment (ASE v3)

For organizations with the highest security and compliance requirements (such as banking, healthcare, or government), the **App Service Environment (ASE v3)** is the premier solution. Unlike multi-tenant plans (Flex, Premium, or Dedicated) that use VNet Integration to "reach into" a network, an ASE is physically deployed **inside** your Virtual Network.

By design, an ASE provides a completely isolated environment where both inbound and outbound IP addresses are dedicated exclusively to your resources and remain permanent for the lifetime of the environment.

---

### Why use an App Service Environment?
*   **True Isolation:** Your Function Apps run on dedicated hardware, not shared with other customers.
*   **Fixed Inbound IP:** A single, static IP for all apps in the environment.
*   **Fixed Outbound IPs:** A specific set of static IPs used for all outbound traffic.
*   **No Multi-tenant Limits:** Higher scaling limits and no "noisy neighbor" concerns.

---

### Prerequisites
1.  **A Large Dedicated Subnet:** ASE v3 requires a subnet with at least a **`/24`** range (256 IPs) and it must be completely empty.
2.  **Isolated v2 (Iv2) Plan:** This is the specific pricing tier required to run functions in an ASE.
3.  **VNet Permissions:** You must have permissions to manage Subnet Delegations.

---

### Step 1: Deploy the App Service Environment v3
1.  In the Azure Portal, search for **App Service Environment** and click **+ Create**.
2.  **Basics Tab:**
    *   **Name:** Give your environment a unique name (e.g., `prod-ase-01`).
    *   **Virtual IP (VIP):** 
        *   Select **External** if you want your static inbound IP to be accessible from the internet.
        *   Select **Internal** if you want the environment to be completely private (ILB).
3.  **Networking Tab:**
    *   Select your **Virtual Network**.
    *   Select your **Dedicated Subnet** (e.g., `snet-ase`).
4.  **Review + Create:** Note that ASE deployment is a heavy operation and can take **1 to 2 hours** to complete.

---

### Step 2: Create an Isolated App Service Plan
Once the ASE is "Ready," you need a plan to host your functions.

1.  Search for **App Service Plans** and click **+ Create**.
2.  **Region/Location:** You must select the **App Service Environment** you just created as the location.
3.  **Pricing Tier:** Select the **Isolated v2 (Iv2)** SKU.
4.  Click **Create**.

---

### Step 3: Deploy the Function App into the ASE
1.  Create a new **Function App**.
2.  **Hosting:** Select the **Isolated v2 Plan** you created in Step 2.
3.  Because the plan is already inside the ASE, the Function App is automatically "born" inside your VNet. No additional VNet integration steps are required.

---

### Step 4: Locating Your Permanent IPs
The IPs assigned to an ASE are environment-wide. Every app inside this ASE shares these specific addresses.

1.  Navigate to your **App Service Environment** resource in the portal.
2.  On the left menu, select **IP Addresses**.
3.  **Inbound IP Address:** This is your permanent static IP for all incoming traffic. You can point your DNS "A Records" here.
4.  **Outbound IP Addresses:** You will see a list of dedicated outbound IPs. These are the IPs you provide to 3rd-party vendors for their firewalls. They will never change as long as the ASE exists.

---

### Comparison: ASE vs. Other Plans
While Flex Consumption and Premium plans use **NAT Gateways** (for outbound) and **Application Gateways** (for inbound) to "simulate" a permanent IP environment, the ASE provides this **natively**. 

| Feature | Flex / Premium / Dedicated | App Service Environment (ASE) |
| :--- | :--- | :--- |
| **Inbound IP** | Via Application Gateway | Native & Permanent |
| **Outbound IP** | Via NAT Gateway | Native & Permanent |
| **Isolation** | Logical (VNet Integration) | Physical (Single-tenant) |
| **Cost** | Mid-range | High (Starting ~$1,000/mo) |
| **Ease of Setup** | Quick | Slow (1-2 hour deployment) |
