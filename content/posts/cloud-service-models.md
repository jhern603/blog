---
title: "The Three Service Models in the Cloud"
date: 2023-07-07T20:44:10-05:00
draft: false
tags: ['cloud-development', 'tips']
---
The cloud is an abstract concept that has been around for a while. It can be generally defined as a platform where you can upload anything, and not have to worry about maintaining the machines behind it.

At a large scale, the cloud can be broken into three models, which we will refer to as subsets.

There are three core subsets of the cloud. Infrastructure as a Service (IaaS), Platform as a Service (PaaS), and Software as a Service (SaaS). There exists many more models than these three, but those are arbitrially created based on what that product's goal is (such as Database as a Service [DBaaS] and Functions as a Service [FaaS]).

## The Cloud is What you Make it
The cloud is a powerful tool for everybody, from everyday users, to developers. From Human Resource workers, to CEOs.

This article focuses on the developer's perspective of the cloud, exploring the three different service models; SaaS, IaaS, and PaaS. This article aims to provide a high-level ideas to help you determine the best solution for your needs, without making you an expert in the cloud.

## Infrastructure as a service
IaaS is a billing and maintenance model adopted by cloud providers like Azure, AWS, GCP, and OCI. It offers the core infrastructure for building applications, including servers, power distribution, cooling systems, networking, security, and other data center aspects.

If this is all provided to you, what isn't? You are still required to setup your Virtual Machines (VMs), Virtual Security Policies, Identity and Access Management (IAM), Operating Systems (OSs), Virtual Cloud Networks (VCNs), and any required middleware.

## Platform as a Service
PaaS is another model that builds off of IaaS. PaaS will provide almost everything except the actual application to run and the data for the application. 

In other words, you do not have to worry about creating and maintaining VCNs, VMs, the OSs for the VMs, creating IAM policies, and more.

A few examples of PaaS are Heroku, Firebase, and Netlify. 

## Software as a Service
Put simply, SaaS is just an entire application that is provided to you. 

SaaS is a good model for businesses that are looking "for something that works." For example, no business wants to spend months developing an HR platform. Instead, they'll look at products such as SalesForce, or Oracle's Taleo.

Both are fully-built HR solutions that are managed by their respective providers. All the business has to do is setup the actual platform schema to fit their model, and import and existing data.

## Which Cloud Model Should I Choose?
There is no such thing as a "one size fits all" solution. Picking which cloud model you want to use depends on how deeply you want your application to integrate with the cloud, as well as how much time and money you want to spend on your application.

Most cloud providers offer a free tier or free trial of their platforms. This provides the opportunity for you to experiment with the different models and see which one fits your needs best.

If you're open to investing more time and potentially reducing costs for application setup, and are comfortable with technical aspects like networking, security, OS management, or if your application follows a microservices architecture with separate teams working on each service, IaaS could be a suitable choice.

If your application is already built, is generally monolothic, and don't want to worry about networking, networking security, and other topics, PaaS might be a good fit.

If your application is already built, follows a monolithic structure, and you prefer not to deal with networking, networking security, and related concerns, PaaS may be a good fit.

Do note, that it is possible to build software that can works in a IaaS model, but not a PaaS and vice versa.

While I provided a brief overview of cloud billing models in this article, it should have introduced high-level ideas to help you choose the best cloud provider for your software.
