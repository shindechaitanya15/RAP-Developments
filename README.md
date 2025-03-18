# RAP-Developments
## Create Root-Child-Grandchild-Great Grandchild relationship using RAP

### Introduction

While discovering RAP, I came across a requirement to create a draft enabled application with Root-Child-Grandchild-Great Grandchild node. While determining feasibility and tips to create such application I was referred to a [Technology Blogs by SAP](https://community.sap.com/t5/technology-blogs-by-sap/bg-p/technology-blog-sap) which explained [How to design managed RAP business objects with 3 or more levels of nodes](https://community.sap.com/t5/technology-blogs-by-sap/how-to-design-managed-rap-business-objects-with-3-or-more-levels-of-nodes/ba-p/13534811) and confirmed the feasibility of such application but took some time to understand all the preparation work while designing the flow of the entities, interface views, projection views and behavior definitions.

In this blog, I will be highlighting my key findings and share sample code created using Eclipse and Demo Objects.

### What is required?

To implement Root-Child-Grand Child-Great Grand Child relationship between entities you will need following things:

1. BTP Trial Account with ABAP Service instance or OnPrem S4HANA which supports RAP\
2. Eclipse\
3. Understanding of RAP Managed/Unmanaged Scenario\
4. Go through [How to design managed RAP business objects with 3 or more levels of nodes](https://community.sap.com/t5/technology-blogs-by-sap/how-to-design-managed-rap-business-objects-with-3-or-more-levels-of-nodes/ba-p/13534811) and RAP( Restful ABAP programming) for [OnPrem S4HANA to get started with the basics](https://community.sap.com/t5/technology-blogs-by-members/rap-restful-abap-programming-for-onprem-s4hana/ba-p/13467651).\
<details open>
<summary>Create Root-Child-Grandchild-Great Grandchild relationship using RAP</summary>
</details>
