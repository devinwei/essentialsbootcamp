.. _flow_isolate_environments:

--------------------------
Flow: 隔离环境
--------------------------

*完成本实验预计30分钟。*

概述
++++++++

在本实验中，你需要创建一个新的环境类别，并将其分配给任务管理器。之后 ，你需要用新建的类别来创建和实现隔离安全策略，以限制未经授权的访问。

隔离环境
++++++++++++++++++++++

当必须完全禁止一组虚拟机与其他一组虚拟机进行通信而没有任何白名单例外时，将会使用隔离策略。 一个常见的示例是使用隔离策略来阻止标记为**Environment: Dev**的虚拟机与**Environment: Production**中的虚拟机进行通信。 如果要在两个组之间创建例外，请不要使用隔离策略，而应使用允许白名单模型的应用程序策略。

在本练习中，你将创建一个新的环境类别并将其分配给任务管理器。 然后，您将创建并实施使用新建的类别的隔离安全策略，以限制未经授权的访问。

创建并分配类别
.................................

#. 在 **Prism Central**, 选择 :fa:`bars` **> Virtual Infrastructure > Categories**.

#. 选中 **Environment** 复选框并点击 **Actions > Update**.

#. 点击最后一个值旁边的 :fa:`plus-circle` 图标，以添加其他类别值。

#. 指定 *Initials*-**Prod** 值的名称.

   .. figure:: images/37.png

#. 点击 **Save**.

#. 在 **Prism Central**, 选择 :fa:`bars` **> Virtual Infrastructure > VMs**.

#. 点击 **Filters** 并搜索 *Initials-* 以显示你的虚拟机。

   .. note::

     如果你先前为应用虚拟机创建了标签，你也可以搜索标签。或者，你可以在过滤器（Filters）面板中搜索**AppType:** *Initials*-**TaskMan**类别。

     .. figure:: images/38.png

#. 使用复选框，选择与应用关联的4台虚拟机(HAProxy, MYSQL, WebServer-0, WebServer-1)，并选择**Actions > Manage Categories**。

#. 在搜索栏指定 **Environment:**\ *Initials*-**Prod** 并点击 **Save** 图标，将类别批量分配给所有4台虚拟机。

   .. figure:: images/39.png

创建隔离策略
............................

#. 在 **Prism Central**, 单击 :fa:`bars` **> Virtual Infrastructure > Policies > Security Policies**.

#. 点击 **Create Security Policy > Isolate Environments (Isolation Policy) > Create**.

#. 填写以下字段:

   - **Name** - *Initials*-Isolate-dev-prod
   - **Purpose** - *Initials* - Isolate dev from prod
   - **Isolate This Category** - Environment:Dev
   - **From This Category** - Environment:*Initials*-Prod
   - Do **NOT** select **Apply this isolation only within a subset of the datacenter**. This option provides additional granularity by only applying to VMs assigned a third, mutual category.

   .. figure:: images/40.png

#. Click **Apply Now** to save the policy and begin enforcement immediately.

#. Return to the *Initials*\ **-WinClient-0** console.

   Is the Task Manager application accessible? Why not?

   Using these simple policies it is possible to block traffic between groups of VMs such as production and development, to isolate a lab system, or provide isolation for compliance.

Deleting a Policy
.................

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > Policies > Security Policies**.

#. Select *Initials*-**Isolate-dev-prod** and click **Actions > Delete**.

#. Type **DELETE** in the confirmation dialogue and click **OK** to disable the policy.

   .. note::

     To disable the policy you can choose to enter **Monitor** mode, rather than deleting the policy completely.

#. Return to the *Initials*\ **-WinClient-0** console and verify the Task Manager application is accessible again from the browser.

Takeaways
+++++++++

- In this exercise you created categories and an isolation security policy with ease without having to alter or change any networking configuration.
- After tagging the VMs with the categories created, the VMs simply behaved according to the policies they belong to.
- The isolation policy is evaluated at a higher priority than the application security policy, and blocks traffic that would be allowed by the application security policy.
