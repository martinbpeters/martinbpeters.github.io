---
id: 3
title: "Setting up PostGIS within Aurora Serverless Postgres using AWS CDK"
date: 2020-02-01
excerpt: Setting up PostGIS within Aurora Serverless Postgres using AWS CDK
category: postgis
tags: postgis postgres ireland aws
comments: true
published: true
---

In my last [blog post](https://www.martinpeters.ie/2020/01/25/ireland-geospatial/) I introduced some geospatial 
analysis concepts and how the Irish population has moved towards Dublin since the mid 1800s. I'll continue on 
this theme in later posts but first I'll introduce the new Cloud Development Kit from AWS and how to provision a 
Postgis enabled Postgres database.

## AWS CDK

The [AWS CDK](https://aws.amazon.com/cdk/) (Amazon Web Services Cloud Development Kit) is a 
[new open source framework](https://aws.amazon.com/about-aws/whats-new/2019/07/the-aws-cloud-development-kit-aws-cdk-is-now-generally-available1/) 
to define cloud infrastructure in code (Infrastructure as Code) and provisioning it through 
[AWS CloudFormation](https://aws.amazon.com/cloudformation/).

CDK provides many high level components to allow rapid code development requiring much less input compared to 
the typical CloudFormation templates. CDK is available in TypeScript, Python, Java and C#. 

I've built a python CDK [repo](https://github.com/martinbpeters/cdk-vpc-postgres) where two stacks provision 
an Amazon Virtual Private Cloud (VPC) with a Bastion host and a Postgres database using Amazon Relational Database 
Service (RDS) (provisioned or serverless). The README of the repo contains details of deploying the stacks and 
accessing the Aurora Serverless postgres database including how you can use AWS secrets and sessions managers to 
access the database instance. The README also contains tons of links to blog posts on the CDK topic.

<script src="https://cdnjs.cloudflare.com/ajax/libs/cloudinary-core/2.8.0/cloudinary-core-shrinkwrap.js"></script>
 
<p> 
  <img data-src="https://res.cloudinary.com/mbp/image/upload/w_auto,c_scale/v1580825372/github/CDK-VPC-Aurora_uypnzg.png" class="cld-responsive">

<script type="text/javascript">
  my_breakpoints = function (width){
    return 50 * Math.ceil(width / 50);
  }
  var cl = cloudinary.Cloudinary.new({cloud_name: "mbp"});
  cl.config({breakpoints:my_breakpoints, responsive_use_breakpoints:"resize"});
  cl.responsive();
</script>
</p>

Hopefully this post and repo has given you a feel for CDK in AWS and how it is now just a couple of 
clicks to get a serverless database up and running. In future posts I'll introduce spatial functions within Postgis, 
spatial datasets available, and explore trends and ways of visualising spatial data. 

## References
1. [AWS CDK](https://aws.amazon.com/cdk/)
2. [AWS CDK Code](https://github.com/aws/aws-cdk)
3. [AWS VPC](https://aws.amazon.com/vpc/)
4. [AWS RDS](https://aws.amazon.com/rds/)
5. [RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html)
6. [List of Postgres Extensions](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html#PostgreSQL.Concepts.General.FeatureSupport.Extensions.11x)
7. [RDS with PostGIS](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Appendix.PostgreSQL.CommonDBATasks.html#Appendix.PostgreSQL.CommonDBATasks.PostGIS)
8. [Amazon Aurora](https://aws.amazon.com/rds/aurora/)
9. [AWS Aurora Serverless](https://aws.amazon.com/rds/aurora/serverless/)
10. [AWS Aurora with Postgres](https://aws.amazon.com/blogs/aws/now-available-amazon-aurora-with-postgresql-compatibility/)
11. [AWS Aurora User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-serverless.html#aurora-serverless.limitations)
12. [Python CDK - VPC + Postgres](https://github.com/martinbpeters/cdk-vpc-postgres)

{% if page.comments %} 
{% include disqus.html %}
{% endif %}
