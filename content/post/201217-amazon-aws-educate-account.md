---
title: "Amazon AWS Starter account 使用体验"
date: 2020-12-17T23:03:38-05:00
draft: false
summary: "实际体验下来用starter账号根据机构的设置会有各种各样的限制，虽然EC2确实很方便"
tags: [AWS]
---
最近学期末忙着各路project和考试，今天总算有时间和心情更新下博客。主要总结下亚马逊的aws使用体验。使用Amazon educate注册之后获得的starter account每年会有100刀的使用额度，如果超了就得自己垫钱。如果只是为了学校里面的项目而用几天，可以省下好多。保险起见可以在创建各种服务之前用[费用计算器](https://calculator.s3.amazonaws.com/index.html)网站来计算一下开销。

由于学校管理员的设置，学生的starter账号没法创建IAM(Identity and Access Management)账号，这让我们的小组作业变得异常难受。IAM账号可以很方便的拿到在aws上创建的资源的访问权限，特别是存储类的RDS, DynamoDB，S3，用户可以不用知道创建资源的人的账号而是用IAM账号来访问。在没有这些的情况下，一些页面就只有创建资源的用户才能看到。RDS在代码里面还可以hard code进地址和访问密码，S3存储则可以让资源变成公开，但DynamoDB如果没有IAM就完全没法让其他人访问。

不得不说亚马逊的EC2服务是真的很方便。如果没有用过EC2,可以理解成云端的虚拟机，具体[差别是这些](https://scotch.io/@Limorw/whats-the-difference-between-vms-and-ec2-instances)。点开创建ec2 instance会先让你选择使用的系统,比如Linux，windows，苹果之类的。我的小组作业用到的是Linux18.04的镜像。然后选择创建instance的类型，t2.nano是默认的免费适用，网速存储cpu都不怎么样。想网速快点再加上更多的ram可以用c4.large，网速类型从low到了median。这篇[网速对比](https://cloudonaut.io/ec2-network-performance-cheat-sheet/)可能会派上用场。之后点击创建，然后设置access rsa key就可以用ssh来远程访问了。下载下来的.pem文件记得`chmod 400 *.pem`改下访问权限。如果ec2想要通过ip地址从公网访问，则只需要在security里面改一下防火墙，加自己需要的inbound rule。比如想允许45555端口，就创建新的custom tcp rule，然后加上45555端口，访问设置成anywhere或者自己想要的ip就可以了。比我之前自己买个树莓派当服务器方便太多了...