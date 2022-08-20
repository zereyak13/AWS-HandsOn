
# Introduction to Amazon ElastiCache
==================================


## Overview
--------

This guide introduces you to Amazon ElastiCache. In this Hands-on you will create an Amazon ElastiCache Redis node and connect to it from an Amazon EC2 instance to run commands. You will then clean up your resources by deleting the stack.

## Topics covered
--------------

By the end of this Hands-on, you will be able to:

-   Create an Amazon ElastiCache cluster
-   Authorize access to your Amazon ElastiCache cluster
-   Connect to your Amazon ElastiCache cluster and run commands
-   Delete an Amazon ElastiCache cluster

## Amazon ElastiCache
------------------

Amazon ElastiCache is a web service that helps improve the performance of web applications by allowing you to retrieve information from fast, managed, in-memory caches, instead of relying entirely on slower
disk-based databases. It supports two common, open-source engines, Memcached and Redis.



## Task 1: Create an Amazon ElastiCache Cluster
--------------------------------------------

In this task, you will create an Amazon ElastiCache cluster.

1.  In the **AWS Management Console**, on the **Services** menu, click **ElastiCache**.

2.  In the navigation menu on the left, click **Redis**.

5.  Click **Create**.

6.  In the **Redis settings** section configure the following:

-   **Name:** ```mycache``` 

-   **Node type:** ```cache.t2.micro```

-   **Description** `my cache memory database`

-   **Engine version compatibility** `6x`

-   **Port**    `6379`

7.  For **Number of replicas**, enter: ```0```

8.  In the **Advanced Redis settings** section, create a new subnet group using these parameters:

-   **Subnet group** `Create new`

-   **Name:** ```mysubnetgroup```

-   **VPC ID:** Select the VPC ID that is also displayed to the left of these instructions.

-   Select the first two subnets.(Subnet'lerden ilk ikisini sec)

9.  Scroll to the bottom of the page.

10. Click **Create**.

You cache cluster will display a **Status** of *creating*. Eventually the **Status** column will display *available* when the cluster is ready. You can proceed to the next task while the cluster is creating.

## Task 2: Authorize Access To Your Amazon ElastiCache Cluster
-----------------------------------------------------------

To connect to your Amazon ElastiCache cluster from an Amazon EC2 instance that is running in the same Amazon VPC, you need to grant network ingress access to the cluster. In this task, you will grant network ingress access to your cluster.

11. In the **Services** menu, click **EC2**.

12. In the navigation pane on the left, click **Security Groups**.

13. *Redis e gel olusturdugun seyin yanindaki oka tikla ve oradaki security group u kopyala* , sonrasinda kopyaladigin security group'u Security Groups arama sekmesine yapistir. 

14. Click the **Inbound rules** tab.

* Burada `all traffic` i gorurseniz silin ve `add rule` a tiklayin.

15. Click Edit ```inbound rules```

![inbound](images/inbound.png)

You will first add a rule to allow your Amazon EC2 instances within your VPC to connect to your ElastiCache nodes.

16. Click Add rule

-   **Type:** *Custom TCP*
-   **Port Range:** (This is the port range for Redis.) ```6379```
-   **Source:** *Anywhere*

17. Click Add rule.

You will now add a rule to allow EC2 instances to connect via SSH to you ElastiCache cluster.

18. For the new rule, configure the following:

-   **Type:** *SSH*
-   **Source:** *Anywhere*

19. For the **All traffic** rule at the top, click Delete

20. Click Save rules

## Task 3: Creating EC2 Instance
----------------------------------------

To connect to your ElastiCache cluster, you need to know what your ElastiCache endpoint is. In this task, you will obtain your ElastiCache endpoint.

In this task, you will: launch an Amazon EC2 instance in your VPC to access your ElastiCache cluster.

25. In the **Services** menu, **EC2**.

26. Click Launch instance \> **Launch instance**.

27. On **Step 1**, click **Select** *Amazon Linux 2 AMI*.

28. On **Step 2**, click **Next: Configure Instance Details**.

You will configure a t2.micro instance.

29. On **Step 3**, configure:

-   **Network:** *Hands-on VPC* ```defaultVPC```
-   Click **Next: Add Storage**.

30. On **Step 4**, click **Next: Add Tags**.

31. On **Step 5**, click **Add Tag**.

32. For the tag details use the following:

-   **Key:** ```Name```
-   **Value:** ```redisInstance``

33. Click **Next: Configure Security Group**.

34. On **Step 6**, configure the following:

```bash
22      Anywhere          0.0.0.0/0

```
-   Click **Review and Launch**

35. On **Step 7**, review the configuration and click **Launch**.

36. On the **Select an existing key pair or create a new key pair**` window, configure the following:

-   Click ** **I acknowledge that ...**
-   Click **Launch Instances**

37. At the **Launch Status** page, click **View Instances**.

38. Wait for your instance to show the following:

-   **Instance State:** ** running
-   **Status Checks:** ** 2/2 checks passed

You can click the **refresh** ** button every 30 seconds to check the status.

21. Go to your ElastiCache Management Console (**Services** menu \> **ElastiCache**)

## Task 4: Obtain Your ElastiCache Endpoint

22. In the navigation pane on the left, click **Redis**.

23. Expand **mycache**

24. Copy the **Primary Endpoint** value to a text editor.

**Do not copy** *:6379*. The copied value should look similar to this: *mycache.drossl.0001.usw2.cache.amazonaws.com*

------------------------------------------------------------------------

39. Copy the **IPv4 Public IP** address to a text editor.

You will use this address in the next task.

## Task 5: Connect To Your EC2 Instance
------------------------------------

In this task, you will connect to your Amazon EC2 instance using SSH.

48. COnnect your EC2 instance with your terminal.

## Task 6: Connect To Your ElastiCache Cluster
-------------------------------------------

In this task, you will connect to your ElastiCache cluster from your EC2 instance using the redis-cli.

First, you need to install the redis-cli. To install the redis-cli, you'll need to install gcc.

55. Install gcc by entering
```bash
sudo yum install gcc -y
```

56. Press **Enter**.

57. Install the redis-cli by entering the following:

```bash
wget http://download.redis.io/redis-stable.tar.gz
tar xvzf redis-stable.tar.gz
cd redis-stable
make
```

58. Connect to your Redis cluster by doing the following:
ls
-   Enter 
```bash 
src/redis-cli -c -h ENDPOINT -p 6379 
```
-   Replace **ENDPOINT** with the name of your Redis endpoint. `Actions/ Copy Nodes/ End Point`
-   Press **Enter**

The full command should look similar to `src/redis-cli -c -h mycache.rdyps9.0001.usw2.cache.amazonaws.com -p 6379`

You should now be connected to the cache node where you can run Redis set a commands.

* Noldu simdi proxy im degisti.

## Task 7: Test Your ElastiCache Cluster {#step11}
-------------------------------------

In this task, you will test out a few commands on your Amazon
ElastiCache cluster.

59. Enter ```set a "Hello" ```

This command sets key **a** with a string value and no expiration.

60. Enter ```get a```

This command gets the value of **a**.

61. Enter ```get b```

This command tries to get the value for **b**, but since it is not in cache, it returns **nil**

62. Enter ```set b "Good-bye" EX 5```

This command sets the value of **b** to "Good-bye" for 5 seconds. Once 5 seconds has passed, **b** will no longer have a value.

63. Enter ```quit```

This command closes the connection to your Redis cluster.

## Task 8: Delete Your Amazon ElastiCache Cluster
----------------------------------------------

In this task, you will delete your Amazon ElastiCache Cluster.

64. Go to the ElastiCache Management Console (**Services** \>  **ElastiCache**)

65. In the navigation menu on the left, click **Redis**.

66. Select ** **mycache**.

67. In the Actions ** menu, click **Delete**

68. In the **Delete Cluster** window, configure:

-   **Create final backup?**: **No**
-   Click **Delete**

The status changes to **deleting**.

As soon as the cache cluster is deleted, you stop incurring charges for that cache cluster.

End Hands-on 
-------

** Congratulations! You now have successfully:

-   Created an Amazon ElastiCache cluster
-   Authorized access to your Amazon ElastiCache cluster
-   Connected to your Amazon ElastiCache cluster using the redis-cli
-   Tested a few Redis commands on your cluster
-   Deleted your Amazon ElastiCache cluster

Additional Resources
--------------------

-   [Amazon ElastiCache](http://aws.amazon.com/elasticache/)
