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
<summary>Step 1</summary>
  
### Step 1
Create relevant Packages and Transport Request as required, start with creating tables for Root, Child, Grand Child and Great Grand Child as shown below:

Root Node
```ABAP
@EndUserText.label : 'Root Node'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_root {
  key client         : abap.clnt not null;
  key uuid_root      : sysuuid_x16 not null;
  semantickey_root   : abap.char(10);
  description        : abap.char(255);
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;
}
```

Child Node
```ABAP
@EndUserText.label : 'Child Node'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_child {
  key client         : abap.clnt not null;
  key uuid_child     : sysuuid_x16 not null;
  semantickey_child  : abap.char(10);
  description        : abap.char(255);
  uuid_root          : sysuuid_x16;
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;
}
```

Grand Child
```ABAP
@EndUserText.label : 'Grand Child Node'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_gchild {
  key client         : abap.clnt not null;
  key uuid_gchild    : sysuuid_x16 not null;
  semantickey_gchild : abap.char(10);
  description        : abap.char(255);
  uuid_root          : sysuuid_x16;
  uuid_child         : sysuuid_x16;
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;
}
```

Great Grand Child
```ABAP
@EndUserText.label : 'Great Grand Child Node'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_ggchild {
  key client          : abap.clnt not null;
  key uuid_ggchild    : sysuuid_x16 not null;
  semantickey_ggchild : abap.char(10);
  description         : abap.char(255);
  uuid_root           : sysuuid_x16;
  uuid_child          : sysuuid_x16;
  uuid_gchild         : sysuuid_x16;
  createdby           : abp_creation_user;
  createdat           : abp_creation_tstmpl;
  changedby           : abp_locinst_lastchange_user;
  lastchangedat       : abp_lastchange_tstmpl;
  locallastchangedat  : abp_locinst_lastchange_tstmpl;
}
```

**_Note:_ Please add only one UUID as key and add reference to Root, Child and Grand Child Keys to respective entities as shown in above tables.**
</details>

<details open>
<summary>Step 2</summary>

### Step 2
Once the tables are created, you can use "Generate ABAP Repository Objects" to quickly generate below objects:

1. View Entities
2. Metadata Extensions
3. Projection Views
4. View Entity Behavior Definition
5. Projection Behavior Definition
6. Service Definition
7. Service Binding

As we are creating Root-Child-Grand Child-Great Grand Child relationship, we only need Objects 4,5,6 and 7 only for the Root Entity. Whereas Object 1,2 and 3 are needed for all 4 Entities. Please create this object manually if "Generate ABAP Repository Objects" functionality is not available for older OnPrem S4HANA versions.

Once you have created all 7 objects for Root Entity, you should be able to preview the Fiori Elements Application from Service Binding. Once the Root Entity is created, you can create remaining objects for Child, Grand Child and Great Grand Child manually.

</details>

<details open>
<summary>Detour</summary>

### Detour

After completing the entire development, during testing I observed that for some weird reason I was not able to navigate to object page of Grand Child and Great Grand Child entity with V4 UI. After spending some additional time trying to resolve the issue, I came across a Question RAP - Unable to Navigate to details object page while going through the solutions I decided to switch from V4 UI to V2 UI and it worked. I am still not sure why did it behaved in such way and will have to explore further. In actual scenario, I added the Object Page manually in Page Map and manifest in BAS and that solved the issue after deployment.

![Service Binding for Root Entity](https://github.com/user-attachments/assets/0ec32a19-4d2a-4cc5-8600-34659cb4efee)
</details>

<details open>
  <summary>Step 3</summary>
  
### Step 3

  Now for the crucial part of this development.

1. Interface Views with Compositions and Associations.
2. Projection Views with "Redirected to parent/ Redirected to composition child".
3. Behavior Definition for Interface Views and Projection.

</details>
