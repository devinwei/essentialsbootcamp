.. _flow_secure_app:

----------------
Flow: 应用安全
----------------

*完成本实验预计需要30分钟*

概述
++++++++

Flow是一个应用为中心的网络安全产品，与Nutanix AHV和Prism Central紧密结合。Flow对运行在AHV上的虚拟机提供了丰富的网络流量可视化 ，自动化和安全功能。
微分段是Flow的组件，使用简单的策略管理来保护虚拟网络安全。使用Prism Central的多个类别（逻辑组），你可以创建功能强大的分布式防火墙，给管理员提供应用为中心的策略管理工具，用以保护虚拟机流量安全。
将其与Calm结合，可以自动化部署创建时就受保护的应用程序。
本实验中，你将创建一个安全策略以限制应用虚拟机之间的通信。

实验设置
+++++++++

本实验取决于多层 **Task Manger** Web应用的可用性。

Refer to the :ref:`taskman` lab for instructions on importing and launching the completed **Task Manager** blueprint. 有关导入和启动已完成的 **Task Manager** 蓝图，参考 :ref:`taskman` 实验说明。 

启动 **Task Manager** 部署后即可开始本实验。 **您无需等待蓝图部署完成即可开始本实验。**  

保护应用程序
+++++++++++++++++++++++

Flow提供了多种开箱即用的系统类比，例如AppType，AppTier和Environment，可以用来快速对虚拟机分组。立即开始使用这些预定义的类别，或添加您自己的类别用来自定义分组。

定义类别值
........................

Prism Central 使用类别来作为元数据，以标记虚拟机，并确定如何应用策略。

#. 在 **Prism Central**, 选择 :fa:`bars` **> Virtual Infrastructure > Categories**.

#. 选择 **AppType** 复选框并单击 **Actions > Update**.

   .. figure:: images/12.png

#. 单击上一个值旁边的 :fa:`plus-circle` 图标，添加额外的类别值。

#. 指定 *Initials*-**TaskMan** 值名称。

   .. figure:: images/13.png

#. 点击 **Save**.

#. 选择 **AppTier** 复选框并点击 **Actions > Update**.

#. 单击上一个值旁边的 :fa:`plus-circle` 图标，添加额外的类别值。

#. 指定 *Initials*-**TMWeb**  值名称。这个类别会被用于应用的Web层。

#. 单击 :fa:`plus-circle` 并指定 *Initials*-**TMDB**. 这个类别会被用于应用的MySQL数据库。

#. 点击 :fa:`plus-circle` 并指定 *Initials*-**TMLB**. 这个类别会用于应用的HAProxy load balancer.

   .. figure:: images/14.png

#. 点击 **Save**.

创建安全策略
..........................

Nutanix Flow包含策略驱动的安全性框架，该框架使用以工作负载为中心的方法，而不是以网络为中心。因此，无论它们的网络配置如何更改以及它们在数据中心中的位置如何，它都可以检查往返VM的流量。 以工作负载为中心，与网络无关的方法还使虚拟化团队能够实施这些安全策略，而不必依赖网络安全团队。

安全策略适用于类别（虚拟机的逻辑分组），而不适用于虚拟机本身。 因此，在给定类别中启动多少个虚拟机是无关紧要的。 类别中虚拟机的相关联的流量可以受到保护，无需任何规模的管理干预。

在你等待Task Manager应用通过Calm蓝图部署的过程中，创建保护应用的安全策略。

#. 在 **Prism Central** 下, 选择 :fa:`bars` **> Policies > Security Policies**.

#. 单击 **Create Security Policy > Secure Applications (App Policy) > Create**.

#. 填写以下字段:

   - **Name** - *Initials*-AppTaskMan
   - **Purpose** - Restrict unnecessary access to Task Manager
   - **Secure this app** - AppType: *Initials*-TaskMan
   -  **不要** 选择 **Filter the app type by category**.

   .. figure:: images/18.png

#. 点击 **Next**.

#. 如果有提示, 在 **Create App Security Policy** 向导教程图上点击 **OK, Got it!** 。

#. 为了更详细配置安全策略，点击 **Set rules on App Tiers** ，而不是对应用所有的组件应用相同的规则。

   .. figure:: images/19.png

#. 点击 **+ Add Tier**.

#. 选择 **AppTier:**\ *Initials*-**TMLB** from the drop down.

#. 对 **AppTier:**\ *Initials*-**TMWeb** 和 **AppTier:**\ *Initials*-**TMDB** 重复 7-8 。

   .. figure:: images/20.png

   接下来你将定义 **Inbound** 规则，该规则定义了允许与你创建的应用程序通讯的源端。你可以允许所有的进站流量，或定义源白名单。默认情况下，安全策略是默认阻止所有进站流量的。

   在本场景下，我们将允许生产网络上所有客户端上端口80的进站TCP流量。

#. 在 **Inbound** 下, 点击 **+ Add Source**.

#. 指定 **Environment:Production** 并点击 **Add**.

   .. note::

     源可以通过IP或子网指定，但类别提供了更多的灵活性，因为数据可以跟随虚拟机而与其网络位置变化无关 。

#. 创建进站策略, 选择 **AppTier:**\ *Initials*-**TMLB** 旁边的图标e **+** 。

   .. figure:: images/21.png

#. 填写以下字段:

   - **Protocol** - TCP
   - **Ports** - 80

   .. figure:: images/22.png

   .. note::

     可以将多协议和端口添加到单个规则。

#. 单击 **Save**.

   Calm的工作流可能也需要访问这些虚拟机，包括横向扩展，纵向扩展，或升级。Calm通过SSH（TCP端口 22）与这些虚拟机通信。

#. 在 **Inbound** 下, 点击 **+ Add Source**.

#. 填写以下字段:

   - **Add source by:** - Select **Subnet/IP**
   - Specify *Your Prism Central IP*\ /32

   .. note::

      **/32** 表示单个IP，而不是一个子网。

   .. figure:: images/23.png

#. 点击 **Add**.

#. 选择 **AppTier:**\ *Initials*-**TMLB** 旁边的图标 **+**  ，指定 **TCP** 端口 **22** 并单击 **Save**.

#. 对 **AppTier:**\ *Initials*-**TMWeb** 和 **AppTier:**\ *Initials*-**TMDB** 重复步骤18，以允许Calm跟Web层和数据库虚拟机通信。

   .. figure:: images/24.png

   默认情况下，安全策略允许应用发送所有出站流量到任意目的地。应用程序唯一需要的出站通信是数据库虚拟机能够与DNS服务器通信。

#. 在  **Outbound**, 从下拉菜单中选择 **Whitelist Only** , 并点击 **+ Add Destination**.

#. 填写以下字段:

   - **Add source by:** - 选择 **Subnet/IP**
   - 指定 *Your Domain Controller IP*\ /32

   .. figure:: images/25.png

#. 点击 **Add**.

#. 选择 **AppTier:**\ *Initials*-**TMDB**右边的 **+** 图标, 指定 **UDP** 端口 **53** 并点击 **Save** ，以允许DNS流量.

   .. figure:: images/26.png

   应用的每一层都与其他层进行通信，该策略必须允许该流量。某些层，如负载均衡器和Web不需要在同一层内进行通信。

#. 定义 intra-app 通信, 点击 **Set Rules within App**.

   .. figure:: images/27.png

#. 点击 **AppTier:**\ *Initials*-**TMLB** 并选择 **No** ，以防止本层虚拟机间的通信。在本层只有一个负载均衡器。

#.  **AppTier:**\ *Initials*-**TMLB** 依旧被选中, 点击 **AppTier:**\ *Initials*-**TMWeb** 右边的 :fa:`plus-circle` 图标创建分层之间的规则.

#. 填写以下字段以允许负载均衡器Web层之间的TCP端口80上的通信：

   - **Protocol** - TCP
   - **Ports** - 80

   .. figure:: images/28.png

#. 点击 **Save**.

#. 点击 **AppTier:**\ *Initials*-**TMWeb** 并选择 **No** 以防止本层虚拟机之间的通讯。当有多个Web虚拟机时，他们之间不需要通信。

#. 当 **AppTier:**\ *Initials*-**TMWeb** 依旧被选中, 点击 **AppTier:**\ *Initials*-**TMDB** 右边的 :fa:`plus-circle` 图标创建另一个分层之间的规则。

#. 填写一下字段以允许TCP端口3306上的通信，从而允许Web服务器和MySQL数据库之间的数据库连接：

   - **Protocol** - TCP
   - **Ports** - 3306

   .. figure:: images/29.png

#. 点击 **Save**.

#. 点击 **Next** 审核安全策略。

#. 点击 **Save and Monitor** 并保存策略。

分配类别值
.........................

.. note::

  By this time, your application blueprint should have finished provisioning. If it has not completed, please wait until it has finished to proceed.

You will now apply the previously created categories to the VMs provisioned from your Task Manager blueprint. Flow categories can be assigned as part of a Calm blueprint, but the purpose of this exercise is to understand category assignment to existing virtual machines in an environment.

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Click **Filters** and search for *Initials-* to display your virtual machines.

   .. figure:: images/15.png

#. Using the checkboxes, select the 4 VMs associated with the application (HAProxy, MYSQL, WebServer-0, WebServer-1) and select **Actions > Manage Categories**.

   .. figure:: images/16.png

   .. note::

     You can also use the **Label** functionality to make searching for this group of VMs faster in the future.

     .. figure:: images/16b.png

#. Specify **AppType:**\ *Initials*-**TaskMan** in the search bar and click **Save** icon to bulk assign the category to all 4 VMs.

#. Select ONLY the *Initials*\ **-HAProxy** VM, select **Actions > Manage Categories**, specify the **AppTier:**\ *Initials*-**TMLB** category and click **Save**.

   .. figure:: images/17.png

#. Repeat Step 5 to assign **AppTier:**\ *Initials*-**TMWeb** to your web tier VMs.

#. Repeat Step 5 to assign **AppTier:**\ *Initials*-**TMDB** to your MySQL VM.

#. Finally, Step 5 to assign **Environment:Dev** to your Windows client VM.

Monitoring and Applying a Security Policy
+++++++++++++++++++++++++++++++++++++++++

Before applying the Flow policy, you will ensure the Task Manager application is working as expected.

Testing the Application
.......................

#. From **Prism Central > Virtual Infrastructure > VMs**, note the IP address of your *Initials*\ **-HAPROXY-0...** and *Initials*\ **-MYSQL-0...** VMs.

#. Launch the console for your *Initials*\ **-WinClient-0** VM.

   This VM was provisioned as part of the Task Manager application blueprint.

#. From the *Initials*\ **-WinClient-0** console open a browser and access \http://*HAPROXY-VM-IP*/.

#. Verify that the application loads and that tasks can be added and deleted.

   .. figure:: images/30.png

#. Open **Command Prompt** and run ``ping -t MYSQL-VM-IP`` to verify connectivity between the client and database. Leave the ping running.

#. Open a second **Command Prompt** and run ``ping -t HAPROXY-VM-IP`` to verify connectivity between the client and load balancer. Leave the ping running.

   .. figure:: images/31.png

Using Flow Visualization
........................

#. Return to **Prism Central** and select :fa:`bars` **> Virtual Infrastructure > Policies > Security Policies >**\ *Initials*-**AppTaskMan**.

#. Verify that **Environment: Dev** appears as an inbound source. The source and line appear in yellow to indicate that traffic has been detected from your client VM.

   .. figure:: images/32.png

#. Mouse over the line connecting **Environment: Dev** to **AppTier:**\ *Initials*-**TMLB** to view the protocol and connection information.

#. Click the yellow flow line to view a graph of connection attempts over the past 24 hours.

   .. figure:: images/33.png

   Are there any other detected outbound traffic flows? Hover over these connections and determine what ports are in use.

#. Click **Update** to edit the policy.

   .. figure:: images/34.png

#. Click **Next** and wait for the detected traffic flows to populate.

#. Mouse over the **Environment: Dev** source that connects to **AppTier:**\ *Initials*-**TMLB** and click the :fa:`check` icon that appears.

   .. figure:: images/35.png

#. Click **OK** to complete adding the rule.

   The **Environment: Dev** source should now turn blue, indicating that it is part of the policy. Mouse over the flow line and verify that both ICMP (ping traffic) and TCP port 80 appear.

#. Click **Next > Save and Monitor** to update the policy.

Applying Flow Policies
......................

In order to enforce the policy you have defined, the policy must be applied.

#. Select *Initials*-**AppTaskMan**  and click **Actions > Apply**.

   .. figure:: images/36.png

#. Type **APPLY** in the confirmation dialogue and click **OK** to begin blocking traffic.

#. Return to the *Initials*\ **-WinClient-0** console.

   What happens to the continuous ping traffic from the Windows client to the database server? Is this traffic blocked?

#. Verify that the Windows Client VM can still access the Task Manager application using the web browser and the load balancer IP address.

   Can you still enter new tasks that require communication between the web server and database?

Takeaways
+++++++++

- Microsegmentation offers additional protection against malicious threats that originate from within the data center and spread laterally, from one machine to another.
- Categories created in Prism Central are available inside Calm blueprints.
- Security policies leverage the text based categories in Prism Central.
- Flow can restrict traffic on certain ports and protocols for VMs running on AHV.
- The policy is created in **Save and Monitor** mode, meaning traffic is not actually blocked until the policy is applied. This is helpful to learn the connections and ensure no traffic is blocked unintentionally.
