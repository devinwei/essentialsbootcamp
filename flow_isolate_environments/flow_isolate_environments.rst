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

#. Click the :fa:`plus-circle` icon beside the last value to add an additional Category value.

#. Specify *Initials*-**Prod** as the value name.

   .. figure:: images/37.png

#. Click **Save**.

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > VMs**.

#. Click **Filters** and search for *Initials-* to display your virtual machines.

   .. note::

     If you previously created a Label for your application VMs you can also search for that label. Alternatively you can search for the **AppType:** *Initials*-**TaskMan** category from the Filters pane.

     .. figure:: images/38.png

#. Using the checkboxes, select the 4 VMs associated with the application (HAProxy, MYSQL, WebServer-0, WebServer-1) and select **Actions > Manage Categories**.

#. Specify **Environment:**\ *Initials*-**Prod** in the search bar and click **Save** icon to bulk assign the category to all 4 VMs.

   .. figure:: images/39.png

Creating an Isolation Policy
............................

#. In **Prism Central**, select :fa:`bars` **> Virtual Infrastructure > Policies > Security Policies**.

#. Click **Create Security Policy > Isolate Environments (Isolation Policy) > Create**.

#. Fill out the following fields:

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
