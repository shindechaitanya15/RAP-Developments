# RAP-Developments
<details>
  <summary>Create Root-Child-Grandchild-Great Grandchild relationship using RAP</summary>

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

![Service Binding for Root Entity](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Service%20Binding%20for%20Root%20Entity.png)
</details>

<details open>
  <summary>Step 3</summary>
  
### Step 3

  Now for the crucial part of this development.

1. Interface Views with Compositions and Associations.
2. Projection Views with "Redirected to parent/ Redirected to composition child".
3. Behavior Definition for Interface Views and Projection.

Interface View for Root Entity
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true
@EndUserText.label: '###GENERATED Core Data Service Entity'
define root view entity ZR_ZT_ROOT
  as select from zzt_root as Root
  composition [*] of ZR_ZT_CHILD as _Child
  association [0..*] to ZR_ZT_GCHILD as _GrandChild on $projection.UuidRoot = _GrandChild.UuidRoot
  association [0..*] to ZR_ZT_GGCHILD as _GreatGrandChild on $projection.UuidRoot = _GreatGrandChild.UuidRoot
{
  key uuid_root as UuidRoot,
  semantickey_root as SemantickeyRoot,
  description as Description,
  @Semantics.user.createdBy: true
  createdby as Createdby,
  @Semantics.systemDateTime.createdAt: true
  createdat as Createdat,
  @Semantics.user.localInstanceLastChangedBy: true
  changedby as Changedby,
  @Semantics.systemDateTime.lastChangedAt: true
  lastchangedat as Lastchangedat,
  @Semantics.systemDateTime.localInstanceLastChangedAt: true
  locallastchangedat as Locallastchangedat,
  _Child,
  _GrandChild,
  _GreatGrandChild
}
```

Interface View for Child Entity
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'View Entity for ZTT_CHILD'
@Metadata.ignorePropagatedAnnotations: true
define view entity ZR_ZT_CHILD
  as select from zzt_child
  association        to parent ZR_ZT_ROOT as _Root            on $projection.UuidRoot = _Root.UuidRoot
  composition [*] of ZR_ZT_GCHILD         as _GrandChild
  association [0..*] to ZR_ZT_GGCHILD     as _GreatGrandChild on $projection.UuidChild = _GreatGrandChild.UuidChild
{
  key uuid_child         as UuidChild,
      semantickey_child  as SemantickeyChild,
      description        as Description,
      uuid_root          as UuidRoot,
      @Semantics.user.createdBy: true
      createdby          as Createdby,
      @Semantics.systemDateTime.createdAt: true
      createdat          as Createdat,
      @Semantics.user.localInstanceLastChangedBy: true
      changedby          as Changedby,
      @Semantics.systemDateTime.lastChangedAt: true
      lastchangedat      as Lastchangedat,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      locallastchangedat as Locallastchangedat,
      _Root,
      _GrandChild,
      _GreatGrandChild
}
```

Interface View for Grandchild Entity
```ABAP
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'View Entity for ZTT_GCHILD'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZR_ZT_GCHILD
  as select from zzt_gchild
  association to ZR_ZT_ROOT         as _Root  on $projection.UuidRoot = _Root.UuidRoot
  association to parent ZR_ZT_CHILD as _Child on $projection.UuidChild = _Child.UuidChild
  composition [*] of ZR_ZT_GGCHILD  as _GreatGrandChild
{
  key uuid_gchild        as UuidGchild,
      semantickey_gchild as SemantickeyGchild,
      description        as Description,
      uuid_root          as UuidRoot,
      uuid_child         as UuidChild,
      @Semantics.user.createdBy: true
      createdby          as Createdby,
      @Semantics.systemDateTime.createdAt: true
      createdat          as Createdat,
      @Semantics.user.localInstanceLastChangedBy: true
      changedby          as Changedby,
      @Semantics.systemDateTime.lastChangedAt: true
      lastchangedat      as Lastchangedat,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      locallastchangedat as Locallastchangedat,
      _Root,
      _Child,
      _GreatGrandChild
}
```

Interface View for Great Grandchild Entity
```ABAP
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'View Entity for ZTT_GGCHILD'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZR_ZT_GGCHILD
  as select from zzt_ggchild
  association to ZR_ZT_ROOT          as _Root       on $projection.UuidRoot = _Root.UuidRoot
  association to ZR_ZT_CHILD         as _Child      on $projection.UuidChild = _Child.UuidChild
  association to parent ZR_ZT_GCHILD as _GrandChild on $projection.UuidGchild = _GrandChild.UuidGchild
{
  key uuid_ggchild        as UuidGgchild,
      semantickey_ggchild as SemantickeyGgchild,
      description         as Description,
      uuid_root           as UuidRoot,
      uuid_child          as UuidChild,
      uuid_gchild         as UuidGchild,
      @Semantics.user.createdBy: true
      createdby           as Createdby,
      @Semantics.systemDateTime.createdAt: true
      createdat           as Createdat,
      @Semantics.user.localInstanceLastChangedBy: true
      changedby           as Changedby,
      @Semantics.systemDateTime.lastChangedAt: true
      lastchangedat       as Lastchangedat,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      locallastchangedat  as Locallastchangedat,
      _Root,
      _Child,
      _GrandChild
}
```

Projection View for Root Entity
```ABAP
@Metadata.allowExtensions: true
@EndUserText.label: 'Root'
@AccessControl.authorizationCheck: #NOT_REQUIRED
define root view entity ZC_ZT_ROOT
  provider contract transactional_query
  as projection on ZR_ZT_ROOT
{
  key UuidRoot,
  SemantickeyRoot,
  Description,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat,
  _Child: redirected to composition child ZC_ZT_CHILD,
  _GrandChild: redirected to ZC_ZT_GCHILD,
  _GreatGrandChild: redirected to ZC_ZT_GGCHILD
}
```

Projection View for Child Entity
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Child'
@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define view entity ZC_ZT_CHILD
 as projection on ZR_ZT_CHILD
{
    key UuidChild,
    SemantickeyChild,
    Description,
    UuidRoot,
    Createdby,
    Createdat,
    Changedby,
    Lastchangedat,
    Locallastchangedat,
    /* Associations */
    _Root: redirected to parent ZC_ZT_ROOT,
    _GrandChild: redirected to composition child ZC_ZT_GCHILD,
    _GreatGrandChild: redirected to ZC_ZT_GGCHILD
}
```

Projection View for Grandchild Entity
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Grand Child'
@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define view entity ZC_ZT_GCHILD
  as projection on ZR_ZT_GCHILD
{
  key UuidGchild,
      SemantickeyGchild,
      Description,
      UuidRoot,
      UuidChild,
      Createdby,
      Createdat,
      Changedby,
      Lastchangedat,
      Locallastchangedat,
      /* Associations */
      _Child           : redirected to parent ZC_ZT_CHILD,
      _GreatGrandChild : redirected to composition child ZC_ZT_GGCHILD,
      _Root            : redirected to ZC_ZT_ROOT
}
```

Projection View for Great Grandchild Entity
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'Great Grand Child'
@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define view entity ZC_ZT_GGCHILD as projection on ZR_ZT_GGCHILD
{
    key UuidGgchild,
    SemantickeyGgchild,
    Description,
    UuidRoot,
    UuidChild,
    UuidGchild,
    Createdby,
    Createdat,
    Changedby,
    Lastchangedat,
    Locallastchangedat,
    /* Associations */
    _Child: redirected to ZC_ZT_CHILD,
    _GrandChild: redirected to parent ZC_ZT_GCHILD,
    _Root: redirected to ZC_ZT_ROOT
}
```

Feel free to implement Metadata Extensions or Add UI Annotation as per your requirement, I have decided to include only one UI Annotation below for Line-Item Reference.

```ABAP
@ui.facet: [ {
    label: 'General Information',
    id: 'GeneralInfo',
    purpose: #STANDARD,
    position: 10 ,
    type: #IDENTIFICATION_REFERENCE
  },
  { id: 'Child',
        purpose: #STANDARD,
        label: 'Grand Child',
        type: #LINEITEM_REFERENCE,
        position: 20,
        targetElement: '_GrandChild'
      }
   ]
```

</details>

<details open>
  <summary>Step 4</summary>

### Step 4

Behavior Definition for Interface Views
```ABAP
managed implementation in class ZBP_R_ZT_ROOT unique;
strict ( 2 );
with draft;
define behavior for ZR_ZT_ROOT alias Root
persistent table zzt_root
draft table zzt_root_d
etag master Locallastchangedat
lock master total etag Lastchangedat
authorization master ( global )

{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed )
  UuidRoot;


  create;
  update;
  delete;

  draft action Activate optimized;
  draft action Discard;
  draft action Edit;
  draft action Resume;
  draft determine action Prepare;

  association _Child { create; with draft; }
  association _GrandChild { with draft; }
  association _GreatGrandChild { with draft; }

  mapping for zzt_root
    {
      UuidRoot           = uuid_root;
      SemantickeyRoot    = semantickey_root;
      Description        = description;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_CHILD alias Child
persistent table zzt_child
draft table zzt_child_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidChild;

  association _Root { with draft; }
  association _GrandChild { create; with draft; }
  association _GreatGrandChild { with draft; }

  update;
  delete;

  mapping for zzt_child
    {
      UuidChild          = uuid_child;
      SemantickeyChild   = semantickey_child;
      Description        = description;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GCHILD alias GrandChild
persistent table zzt_gchild
draft table zzt_gchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGchild;

  ancestor association _Root { with draft; }
  association _Child { with draft; }
  association _GreatGrandChild { create; with draft; }

  update;
  delete;

  mapping for zzt_gchild
    {
      UuidGChild         = uuid_gchild;
      UuidChild          = uuid_child;
      SemantickeyGchild  = semantickey_gchild;
      Description        = description;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GGCHILD alias GreatGranChild
persistent table zzt_ggchild
draft table zzt_ggchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  UuidGchild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGgchild;

  ancestor association _Root { with draft; }
  ancestor association _Child { with draft; }
  association _GrandChild { with draft; }

  update;
  delete;

  mapping for zzt_ggchild
    {
      UuidGgchild        = uuid_ggchild;
      SemantickeyGgchild = semantickey_ggchild;
      Description        = description;
      UuidRoot           = uuid_root;
      UuidChild          = uuid_child;
      UuidGChild         = uuid_gchild;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}
```

Behavior Definition of Projection View
```ABAP
projection implementation in class ZBP_C_ZT_ROOT unique;
strict ( 2 );
use draft;
define behavior for ZC_ZT_ROOT alias Root
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;
  use association _Child { create; with draft; }
  use association _GrandChild { with draft; }
  use association _GreatGrandChild { with draft; }
}

define behavior for ZC_ZT_CHILD alias Child
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _GrandChild { create; with draft; }
  use association _GreatGrandChild { with draft; }
}
define behavior for ZC_ZT_GCHILD alias GrandChild
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _Child { with draft; }
  use association _GreatGrandChild { create; with draft; }
}
define behavior for ZC_ZT_GGCHILD alias GreatGrandChild
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _Child { with draft; }
  use association _GrandChild { with draft; }
}
```

Please pay attention to how the association are defined in each behavior definition, this along with Composition and Associations of Interface View, Redirected to Parent and Redirected to Composition Child annotations of Projection view will define how the relationship between Root, Child, Grandchild and Great Grandchild will behave. If you face any issues with activation of Behavior Definition, Service Definition or Service Binding then revisit the Interface and Projection views of each entity. Once everything is set and activated, you can refresh the Service Biding and view the complete hierarchy and relationship between the various entities created. 

For any entity that is missing from Service Binding, check Service Definition and make sure expose the correct Projection view. Refer below for sample, additionally here you can add your own F4 Helps, additional entities as well in actual scenario.

### Service Definition

```ABAP
@EndUserText.label: 'Service Binding for Root Entity V2'
define service ZUI_ZT_ROOT_V2 {
  expose ZC_ZT_ROOT    as Root;
  expose ZC_ZT_CHILD   as Child;
  expose ZC_ZT_GCHILD  as GrandChild;
  expose ZC_ZT_GGCHILD as GreatGrandChild;
}
```

### Service Binding
![Service Binding oData V2 UI](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Service%20Binding%20oData%20V2%20UI.png)
</details>

<details open>
  <summary>Step 5</summary>

### Step 5

### Final Preview of Fiori Application

### Fiori List Report Page

![Fiori List Report Page](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Fiori%20List%20Report.png)

### Root Entity

![Root Entity](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Root%20Entity.png)

### Child Entity

![Child Entity](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Child%20Entity.png)

### Grand Child

![Grand Child](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Grand%20Child.png)

### Great Grand Child

![Great Grand Child](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Great%20Grand%20Child.png)
</details>

</details>

<details>
  <summary>Precheck Methods, Validations, Determinations, Read and Modify EML Statements in RAP</summary>
  
## Precheck Methods, Validations, Determinations, Read and Modify EML Statements in RAP
  
### Introduction

In continuation to my previous [Technology Blogs by SAP](https://community.sap.com/t5/technology-blogs-by-sap/bg-p/technology-blog-sap) where we discussed how to [Create Root-Child-Grandchild-Great Grandchild relationship using RAP](https://community.sap.com/t5/technology-blogs-by-members/create-root-child-grandchild-great-grandchild-relationship-using-rap/ba-p/14029438), with same example as base we will implement Precheck Methods, Validation, Determination, Read and Modify EML Statements in ABAP RESTful Application Programming Model. 

<details open>
  <summary>Precheck Methods</summary>
  
### Precheck Methods

Precheck methods can be defined in Behavior Definition and are triggered during relevant actions. This can be used to validate the data before processing the action. Here in the example, I am implementing Precheck on Delete action to prevent deletion if child entities are created.

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
  valid              : abap_boolean;
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;

}
```

![Adding Delete Precheck in Behavior Definition](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Adding%20Delete%20Precheck%20in%20Behavior%20Definition.png)

You can use Quick Fix functionality using "Ctrl + 1" and implement below code inside the method. Once implemented, activate and test code by trying to delete the root entity, while deleting an error should appear.

```ABAP
METHOD precheck_delete.
    READ ENTITIES OF zr_zt_root IN LOCAL MODE
    ENTITY Root
    ALL FIELDS WITH CORRESPONDING #( keys )
    RESULT DATA(lt_root)
    ENTITY Root BY \_Child
    ALL FIELDS WITH CORRESPONDING #( keys )
    RESULT DATA(lt_child).
    IF lt_child[] IS NOT INITIAL.
      LOOP AT lt_root ASSIGNING FIELD-SYMBOL(<lfs_root>).
        APPEND VALUE #( %tky = <lfs_root>-%tky ) TO failed-root.

        APPEND VALUE #( %tky = keys[ 1 ]-%tky
                        %msg = new_message_with_text(
                                 severity = if_abap_behv_message=>severity-error
                                 text     = 'Can not delete Root entity as it has child'
                               ) ) TO reported-root.
      ENDLOOP.
    ENDIF.
  ENDMETHOD.
```

An error will be displayed whenever Precheck validation fails:

![Delete Precheck Error](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Delete%20Precheck%20Error.png)
</details>

<details open>
  <summary>Validations and Determinations</summary>
  
### Validations and Determinations

For Validations and Determination, I have added two "abap_boolean" flags in all the entities like below. Make sure that you add the flag in Table, Draft Table, Interface View, Projection View and Behavior definition that we have created in the previous blog.

Note 1: After adding the fields and changing the projection the view, preview for the Fiori Application stopped working. You may get error like "Creating operations are disabled for entity $AE_UIX#SRVD#XXXXXXXX" while creating or updating.

For me issue was resolved after recreating the Behavior Definition for Projection View and Unpublishing and Publishing of oData V2 Service Binding.

Note 2: In below examples, I have implemented logic for Root and Child entity only. Same can be used to Grandchild and Great Grand Child Entities. For Interface and Projection View, I have shown only Root Entities code as remaining entities, tables will also have the same changes for fields.

Root Table:
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
  valid              : abap_boolean;
  active             : abap_boolean;
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;

}
```

Root Draft Table:
```ABAP
@EndUserText.label : 'Draft Database Table for ZZT_ROOT_D'
@AbapCatalog.enhancement.category : #EXTENSIBLE_ANY
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_root_d {

  key mandt          : mandt not null;
  key uuidroot       : sysuuid_x16 not null;
  semantickeyroot    : abap.char(10);
  description        : abap.char(255);
  valid              : abap_boolean;
  active             : abap_boolean;
  createdby          : abp_creation_user;
  createdat          : abp_creation_tstmpl;
  changedby          : abp_locinst_lastchange_user;
  lastchangedat      : abp_lastchange_tstmpl;
  locallastchangedat : abp_locinst_lastchange_tstmpl;
  "%admin"           : include sych_bdl_draft_admin_inc;

}
```

Root Interface View:
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true
@EndUserText.label: '###GENERATED Core Data Service Entity'
define root view entity ZR_ZT_ROOT
  as select from zzt_root as Root
  composition [*] of ZR_ZT_CHILD as _Child
  association [0..*] to ZR_ZT_GCHILD as _GrandChild on $projection.UuidRoot = _GrandChild.UuidRoot
  association [0..*] to ZR_ZT_GGCHILD as _GreatGrandChild on $projection.UuidRoot = _GreatGrandChild.UuidRoot
{
  key uuid_root as UuidRoot,
  semantickey_root as SemantickeyRoot,
  description as Description,
  valid as Valid,
  active as Active,
  @Semantics.user.createdBy: true
  createdby as Createdby,
  @Semantics.systemDateTime.createdAt: true
  createdat as Createdat,
  @Semantics.user.localInstanceLastChangedBy: true
  changedby as Changedby,
  @Semantics.systemDateTime.lastChangedAt: true
  lastchangedat as Lastchangedat,
  @Semantics.systemDateTime.localInstanceLastChangedAt: true
  locallastchangedat as Locallastchangedat,
  _Child,
  _GrandChild,
  _GreatGrandChild
}
```

Root Projection View:
```ABAP
@Metadata.allowExtensions: true
@EndUserText.label: 'Root'
@AccessControl.authorizationCheck: #NOT_REQUIRED
define root view entity ZC_ZT_ROOT
  provider contract transactional_query
  as projection on ZR_ZT_ROOT
{
  key UuidRoot,
  SemantickeyRoot,
  Description,
  Valid,
  Active,
  Createdby,
  Createdat,
  Changedby,  
  Lastchangedat,
  Locallastchangedat,
  _Child: redirected to composition child ZC_ZT_CHILD,
  _GrandChild: redirected to ZC_ZT_GCHILD,
  _GreatGrandChild: redirected to ZC_ZT_GGCHILD
}
```

Behavior Definition:
```ABAP
managed implementation in class ZBP_R_ZT_ROOT unique;
strict ( 2 );
with draft;
define behavior for ZR_ZT_ROOT alias Root
persistent table zzt_root
draft table zzt_root_d
etag master Locallastchangedat
lock master total etag Lastchangedat
authorization master ( global )

{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed )
  UuidRoot;


  create;
  update;
  delete ( precheck );

  draft action Activate optimized;
  draft action Discard;
  draft action Edit;
  draft action Resume;
  draft determine action Prepare
  {
    validation ( always ) checkValid;
    determination ( always ) determineActive;
  }

  /*Validation*/
  validation checkValid on save { create; update; }
  /*Determination*/
  determination determineActive on save { create; update; }

  association _Child { create; with draft; }
  association _GrandChild { with draft; }
  association _GreatGrandChild { with draft; }

  mapping for zzt_root
    {
      UuidRoot           = uuid_root;
      SemantickeyRoot    = semantickey_root;
      Description        = description;
      Valid              = valid;
      Active             = active;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_CHILD alias Child
persistent table zzt_child
draft table zzt_child_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidChild;

  association _Root { with draft; }
  association _GrandChild { create; with draft; }
  association _GreatGrandChild { with draft; }

  update;
  delete;

  mapping for zzt_child
    {
      UuidChild          = uuid_child;
      SemantickeyChild   = semantickey_child;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GCHILD alias GrandChild
persistent table zzt_gchild
draft table zzt_gchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGchild;

  ancestor association _Root { with draft; }
  association _Child { with draft; }
  association _GreatGrandChild { create; with draft; }

  update;
  delete;

  mapping for zzt_gchild
    {
      UuidGChild         = uuid_gchild;
      UuidChild          = uuid_child;
      SemantickeyGchild  = semantickey_gchild;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GGCHILD alias GreatGranChild
persistent table zzt_ggchild
draft table zzt_ggchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  UuidGchild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGgchild;

  ancestor association _Root { with draft; }
  ancestor association _Child { with draft; }
  association _GrandChild { with draft; }

  update;
  delete;

  mapping for zzt_ggchild
    {
      UuidGgchild        = uuid_ggchild;
      SemantickeyGgchild = semantickey_ggchild;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      UuidChild          = uuid_child;
      UuidGChild         = uuid_gchild;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}
```

</details>

<details open>
  <summary>EML Read Statements</summary>
  
### EML Read Statements

READ ENTITES OF.... can be used to read associated entities by using association names as shown below. Here, Root entity's keys are being used to fetch Child entity's details with the help of "ENTITY Root BY \_Child", similarly it can be done for other associations as well.

```ABAP
READ ENTITIES OF zr_zt_root IN LOCAL MODE
    ENTITY Root
    ALL FIELDS WITH CORRESPONDING #( keys )
    RESULT DATA(lt_root)
    ENTITY Root BY \_Child
    ALL FIELDS WITH CORRESPONDING #( keys )
    RESULT DATA(lt_child).
```

</details>

<details open>
  <summary>EML Modify Statement</summary>
  
### EML Modify Statement

For Modify Statement, a Table for Update needs to be declared and filled with appropriate key, draft flag, field values and control structure flags. Additionally, in modify statement you can mention the fields you want to update explicitly for successful update.

```ABAP
*Update Table declaration
DATA: lt_child_update  TYPE TABLE FOR UPDATE zr_zt_child.

*Fill update table with valid values and control flags
      LOOP AT lt_root ASSIGNING FIELD-SYMBOL(<lfs_root>).

        LOOP AT lt_child[] ASSIGNING FIELD-SYMBOL(<lfs_child>) WHERE Active <> <lfs_root>-Active.

          APPEND VALUE #( %key = <lfs_child>-%key
                          %is_draft = <lfs_child>-%is_draft
                          active = <lfs_root>-Active
                          %control = VALUE #( active = if_abap_behv=>mk-on ) ) TO lt_child_update.
        ENDLOOP.

      ENDLOOP.


*Modify
      IF lt_child[] IS NOT INITIAL.
        MODIFY ENTITIES OF zr_zt_root IN LOCAL MODE
        ENTITY Child
        UPDATE FIELDS ( active )
        WITH lt_child_update
        REPORTED DATA(mappedchild).

        reported = CORRESPONDING #( DEEP mappedchild ).
      ENDIF.
```

</details>

<details open>
  <summary>Validation Logic</summary>
  
### Validation Logic

For Validation, I have implemented below logic. Expectation is user should mark Child entity as valid before marking Root Entity as valid. This logic is only implemented for Root-Child Entity, you can similarly implement for Grandchild and Great Grandchild entities as well.

```ABAP
METHOD checkValid.
    READ ENTITIES OF zr_zt_root IN LOCAL MODE
      ENTITY Root
      ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(lt_root)
      ENTITY Root BY \_Child
      ALL FIELDS WITH CORRESPONDING #( keys )
      RESULT DATA(lt_child).
    IF lt_child[] IS NOT INITIAL.
      LOOP AT lt_root ASSIGNING FIELD-SYMBOL(<lfs_root>).
        IF <lfs_root>-Valid IS NOT INITIAL.

          LOOP AT lt_child[] ASSIGNING FIELD-SYMBOL(<lfs_child>) WHERE Valid IS INITIAL.
            APPEND VALUE #( %tky = <lfs_root>-%tky ) TO failed-root.

            APPEND VALUE #( %tky = keys[ 1 ]-%tky
                            %msg = new_message_with_text(
                                     severity = if_abap_behv_message=>severity-error
                                     text     = 'Child entity is not marked as valid'
                                   ) ) TO reported-root.
          ENDLOOP.

        ENDIF.
      ENDLOOP.
    ENDIF.
  ENDMETHOD.
```

On Save, the validation will be triggered if the Root entity is marked as valid:

![Validation triggered on save](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Validation%20triggered%20on%20save.png)

![Saved after successful validation](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Saved%20after%20successful%20validation.png)

</details>

<details open>
  <summary>Determination Logic</summary>
  
### Determination Logic

For Determination, expectation is that once Root Entity is marked as active, it should replicate to child entity as well. Below logic is implemented to replicate the active flag to associated entities.

```ABAP
METHOD determineActive.
    DATA: lt_child_update  TYPE TABLE FOR UPDATE zr_zt_child.
    READ ENTITIES OF zr_zt_root IN LOCAL MODE
        ENTITY Root
        ALL FIELDS WITH CORRESPONDING #( keys )
        RESULT DATA(lt_root)
        ENTITY Root BY \_Child
        ALL FIELDS WITH CORRESPONDING #( keys )
        RESULT DATA(lt_child).
    IF lt_child[] IS NOT INITIAL.
      LOOP AT lt_root ASSIGNING FIELD-SYMBOL(<lfs_root>).

        LOOP AT lt_child[] ASSIGNING FIELD-SYMBOL(<lfs_child>) WHERE Active <> <lfs_root>-Active.

          APPEND VALUE #( %key = <lfs_child>-%key
                          %is_draft = <lfs_child>-%is_draft
                          active = <lfs_root>-Active
                          %control = VALUE #( active = if_abap_behv=>mk-on ) ) TO lt_child_update.
        ENDLOOP.

      ENDLOOP.

      IF lt_child[] IS NOT INITIAL.
        MODIFY ENTITIES OF zr_zt_root IN LOCAL MODE
        ENTITY Child
        UPDATE FIELDS ( active )
        WITH lt_child_update
        REPORTED DATA(mappedchild).

        reported = CORRESPONDING #( DEEP mappedchild ).
      ENDIF.
    ENDIF.
  ENDMETHOD.
```

On Save, the determination will be triggered, and it will update the child entity with value same as root entity:

![Determination for Active Checkbox](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/Determination%20for%20Active%20Checkbox.png)

</details>

</details>

<details>
  <summary>Add attachments to RAP entities</summary>

## Add attachments to RAP entities

### Introduction
In continuation to the previous blogs about how to [Create Root-Child-Grandchild-Great Grandchild relationship using RAP](https://community.sap.com/t5/technology-blogs-by-members/create-root-child-grandchild-great-grandchild-relationship-using-rap/ba-p/14029438), with same example as base we will implement attachment handling in ABAP RESTful Application Programming Model.

### What is required?
1.	Go through [Create Root-Child-Grandchild-Great Grandchild relationship using RAP](https://community.sap.com/t5/technology-blogs-by-members/create-root-child-grandchild-great-grandchild-relationship-using-rap/ba-p/14029438), create required entities.
2.	If you are using your own example, create all the relevant objects and have a Fiori Preview Application ready for testing

### Step 1
Create a Database Table to store he attachments.
```ABAP
@EndUserText.label : 'PDF Attachments for Root'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zzt_root_pdf {

  key client          : abap.clnt not null;
  key uuid_attachment : sysuuid_x16 not null;
  uuid_root           : sysuuid_x16 not null;
  description         : abap.char(255);
  mimetype            : abap.string(0);
  filename            : abap.string(0);
  value               : abap.rawstring(0);
  createdby           : abp_creation_user;
  createdat           : abp_creation_tstmpl;
  changedby           : abp_locinst_lastchange_user;
  lastchangedat       : abp_lastchange_tstmpl;
  locallastchangedat  : abp_locinst_lastchange_tstmpl;

}
```
### Step 2
Create Interface View for the Attachment Table.
```ABAP
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'PDF Attachment for Root'
@Metadata.ignorePropagatedAnnotations: true
@ObjectModel.usageType:{
    serviceQuality: #X,
    sizeCategory: #S,
    dataClass: #MIXED
}
define view entity ZR_ZT_ROOT_PDF
  as select from zzt_root_pdf
  association to parent ZR_ZT_ROOT as _Root on $projection.UuidRoot = _Root.UuidRoot
{
  key uuid_attachment    as UuidAttachment,
      uuid_root          as UuidRoot,
      description        as Description,
      mimetype           as Mimetype,
      filename           as Filename,
      value              as Value,
      @Semantics.user.createdBy: true
      createdby          as Createdby,
      @Semantics.systemDateTime.createdAt: true
      createdat          as Createdat,
      @Semantics.user.localInstanceLastChangedBy: true
      changedby          as Changedby,
      @Semantics.systemDateTime.lastChangedAt: true
      lastchangedat      as Lastchangedat,
      @Semantics.systemDateTime.localInstanceLastChangedAt: true
      locallastchangedat as Locallastchangedat,
      _Root
}
```
### Step 3
Create Projection View for the Attachment Table. 

Here, ``` @Semantics.largeObject: { fileName: 'Filename', mimeType: 'Mimetype', contentDispositionPreference: #INLINE, acceptableMimeTypes: [ 'application/pdf' ]  } ``` plays key role in defining how the attachment entity behaves in Fiori Application. 

``` contentDispositionPreference ``` will determine the attachment behaviour, which is download or preview based on ``` #INLINE ``` or ``` #ATTACHMENT ``` value.

``` filename ``` and ``` mimeType ``` will store the file's relevant properties.

``` acceptableMimeTypes ``` will restrict the mime types shown in the File Uploader prompt.


```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: 'PDF Attachment'
@Metadata.ignorePropagatedAnnotations: true
@Metadata.allowExtensions: true
define view entity ZC_ZT_ROOT_PDF
  as projection on ZR_ZT_ROOT_PDF
{
  key UuidAttachment,
      UuidRoot,
      Description,
      @Semantics.mimeType: true
      Mimetype,
      Filename,
      @Semantics.largeObject: { fileName: 'Filename', mimeType: 'Mimetype', contentDispositionPreference: #INLINE, acceptableMimeTypes: [ 'application/pdf' ]  }
      Value,
      Createdby,
      Createdat,
      Changedby,
      Lastchangedat,
      Locallastchangedat,
      /* Associations */
      _Root : redirected to parent ZC_ZT_ROOT
}
```
### Step 4
Add associations and Parent-Child Relationship in Interface and Projection View of root entity.

Root Entity Interface View
```ABAP
@AccessControl.authorizationCheck: #NOT_REQUIRED
@Metadata.allowExtensions: true
@EndUserText.label: '###GENERATED Core Data Service Entity'
define root view entity ZR_ZT_ROOT
  as select from zzt_root as Root
  composition [*] of ZR_ZT_CHILD as _Child
  composition [*] of ZR_ZT_ROOT_PDF as _Pdf
  association [0..*] to ZR_ZT_GCHILD as _GrandChild on $projection.UuidRoot = _GrandChild.UuidRoot
  association [0..*] to ZR_ZT_GGCHILD as _GreatGrandChild on $projection.UuidRoot = _GreatGrandChild.UuidRoot
{
  key uuid_root as UuidRoot,
  semantickey_root as SemantickeyRoot,
  description as Description,
  valid as Valid,
  active as Active,
  @Semantics.user.createdBy: true
  createdby as Createdby,
  @Semantics.systemDateTime.createdAt: true
  createdat as Createdat,
  @Semantics.user.localInstanceLastChangedBy: true
  changedby as Changedby,
  @Semantics.systemDateTime.lastChangedAt: true
  lastchangedat as Lastchangedat,
  @Semantics.systemDateTime.localInstanceLastChangedAt: true
  locallastchangedat as Locallastchangedat,
  _Child,
  _GrandChild,
  _GreatGrandChild,
  _Pdf
}
```
Root Entity Projection View
```ABAP
@Metadata.allowExtensions: true
@EndUserText.label: 'Root'
@AccessControl.authorizationCheck: #NOT_REQUIRED
define root view entity ZC_ZT_ROOT
  provider contract transactional_query
  as projection on ZR_ZT_ROOT
{
  key UuidRoot,
      SemantickeyRoot,
      Description,
      Valid,
      Active,
      Createdby,
      Createdat,
      Changedby,
      Lastchangedat,
      Locallastchangedat,
      _Child           : redirected to composition child ZC_ZT_CHILD,
      _GrandChild      : redirected to ZC_ZT_GCHILD,
      _GreatGrandChild : redirected to ZC_ZT_GGCHILD,
      _Pdf             : redirected to composition child ZC_ZT_ROOT_PDF
}
```

### Step 5
Update Behaviour Definition for Interface Views and Projection Views

Behaviour Definition for Interface Views
```ABAP
managed implementation in class ZBP_R_ZT_ROOT unique;
strict ( 2 );
with draft;
define behavior for ZR_ZT_ROOT alias Root
persistent table zzt_root
draft table zzt_root_d
etag master Locallastchangedat
lock master total etag Lastchangedat
authorization master ( global )

{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed )
  UuidRoot;


  create;
  update;
  delete ( precheck );

  draft action Activate optimized;
  draft action Discard;
  draft action Edit;
  draft action Resume;
  draft determine action Prepare
  {
    validation ( always ) checkValid;
    determination ( always ) determineActive;
  }

  /*Validation*/
  validation checkValid on save { create; update; }
  /*Determination*/
  determination determineActive on save { create; update; }

  association _Child { create; with draft; }
  /*Added Attachments*/
  association _Pdf { create; with draft; }
  association _GrandChild { with draft; }
  association _GreatGrandChild { with draft; }

  mapping for zzt_root
    {
      UuidRoot           = uuid_root;
      SemantickeyRoot    = semantickey_root;
      Description        = description;
      Valid              = valid;
      Active             = active;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_CHILD alias Child
persistent table zzt_child
draft table zzt_child_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidChild;

  association _Root { with draft; }
  association _GrandChild { create; with draft; }
  association _GreatGrandChild { with draft; }

  update;
  delete;

  mapping for zzt_child
    {
      UuidChild          = uuid_child;
      SemantickeyChild   = semantickey_child;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GCHILD alias GrandChild
persistent table zzt_gchild
draft table zzt_gchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGchild;

  ancestor association _Root { with draft; }
  association _Child { with draft; }
  association _GreatGrandChild { create; with draft; }

  update;
  delete;

  mapping for zzt_gchild
    {
      UuidGChild         = uuid_gchild;
      UuidChild          = uuid_child;
      SemantickeyGchild  = semantickey_gchild;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}

define behavior for ZR_ZT_GGCHILD alias GreatGranChild
persistent table zzt_ggchild
draft table zzt_ggchild_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  UuidChild,
  UuidGchild,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidGgchild;

  ancestor association _Root { with draft; }
  ancestor association _Child { with draft; }
  association _GrandChild { with draft; }

  update;
  delete;

  mapping for zzt_ggchild
    {
      UuidGgchild        = uuid_ggchild;
      SemantickeyGgchild = semantickey_ggchild;
      Description        = description;
      Valid              = valid;
      Active             = active;
      UuidRoot           = uuid_root;
      UuidChild          = uuid_child;
      UuidGChild         = uuid_gchild;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}
/*Added Attachments*/
define behavior for ZR_ZT_ROOT_PDF alias PdfAttachment
persistent table zzt_root_pdf
draft table zzt_root_pdf_d
lock dependent by _Root
authorization dependent by _Root
etag master Lastchangedat
{
  field ( readonly )
  UuidRoot,
  Createdby,
  Createdat,
  Changedby,
  Lastchangedat,
  Locallastchangedat;

  field ( numbering : managed, readonly ) UuidAttachment;

  association _Root { with draft; }

  update;
  delete;

  mapping for zzt_root_pdf
    {
      UuidAttachment     = uuid_attachment;
      Description        = description;
      UuidRoot           = uuid_root;
      Mimetype           = mimetype;
      Value              = value;
      Filename           = filename;
      Createdby          = createdby;
      Createdat          = createdat;
      Changedby          = changedby;
      Lastchangedat      = lastchangedat;
      Locallastchangedat = locallastchangedat;
    }
}
```

Behaviour Definition for Consumption Views
```ABAP
projection implementation in class ZBP_C_ZT_ROOT unique;
strict ( 2 );
use draft;
define behavior for ZC_ZT_ROOT alias Root
use etag

{
  use create;
  use update;
  use delete;

  use action Edit;
  use action Activate;
  use action Discard;
  use action Resume;
  use action Prepare;

  use association _Child { create; with draft; }
  /*Added Attachments*/
  use association _Pdf { create; with draft; }
  use association _GrandChild { with draft; }
  use association _GreatGrandChild { with draft; }
}

define behavior for ZC_ZT_CHILD alias Child
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _GrandChild { create; with draft; }
  use association _GreatGrandChild { with draft; }
}
define behavior for ZC_ZT_GCHILD alias GrandChild
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _Child { with draft; }
  use association _GreatGrandChild { create; with draft; }
}
define behavior for ZC_ZT_GGCHILD alias GreatGrandChild
{
  use update;
  use delete;

  use association _Root { with draft; }
  use association _Child { with draft; }
  use association _GrandChild { with draft; }
}
/*Added Attachments*/
define behavior for ZC_ZT_ROOT_PDF alias PdfAttachment
{
  use update;
  use delete;

  use association _Root { with draft; }
}
```

### Step 6

Create and Update Metadata Extensions for Fiori Preview

Create metadata extension for PDF Attachment entity

```ABAP
@Metadata.layer: #CORE
@UI.headerInfo.title.type: #STANDARD
@UI.headerInfo.title.value: 'Filename'
@UI.headerInfo.description.type: #STANDARD
@UI.headerInfo.description.value: 'UuidAttachment'
annotate entity ZC_ZT_ROOT_PDF
    with 
{
   @UI.facet: [ {
  label: 'General Information',
  id: 'GeneralInfo',
  purpose: #STANDARD,
  position: 10 ,
  type: #IDENTIFICATION_REFERENCE
  },
  {
          id: 'ChangeDetails',
          purpose: #STANDARD,
          label: 'Change Log',
          type: #COLLECTION,
          position: 30
     },
     {
        id : 'ChangeInfo',
        label: 'Change Log',
        purpose: #STANDARD,
        parentId : 'ChangeDetails',
        type : #FIELDGROUP_REFERENCE,
        targetQualifier : 'ChangeInformation',
        position: 40
    }
  ]
  @UI.identification: [ {
   position: 10
  } ]
  @UI.lineItem: [ {
    position: 10
  } ]
  @UI.selectionField: [ {
    position: 10
  } ]
  UuidAttachment;
  @UI.identification: [ {
  position: 20
  } ]
  @UI.lineItem: [ {
    position: 20
  } ]
  @UI.selectionField: [ {
    position: 20
  } ]
  @EndUserText: { label: 'File Name', quickInfo: 'File Name' }
  @UI.hidden: true
  Filename;
  @UI.identification: [ {
  position: 30
  } ]
  @UI.lineItem: [ {
    position: 30
  } ]
  @UI.selectionField: [ {
    position: 30
  } ]
  @EndUserText: { label: 'Description', quickInfo: 'Description' }
  Description;
  @UI.identification: [ {
  position: 40
  } ]
  @UI.lineItem: [ {
    position: 40
  } ]
  @UI.selectionField: [ {
    position: 40
  } ]
  UuidRoot;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 10 }]
  @UI.lineItem: [ {
    position: 70
  } ]
  @UI.selectionField: [ {
    position: 70
  } ]
  Createdby;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 20 }]
  @UI.lineItem: [ {
    position: 80
  } ]
  @UI.selectionField: [ {
    position: 80
  } ]
  Createdat;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 30 }]
  @UI.lineItem: [ {
    position: 90
  } ]
  @UI.selectionField: [ {
    position: 90
  } ]
  Changedby;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 40 }]
  @UI.lineItem: [ {
    position: 100
  } ]
  @UI.selectionField: [ {
    position: 100
  } ]
  Lastchangedat;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 50 }]
  @UI.lineItem: [ {
    position: 110
  } ]
  @UI.selectionField: [ {
    position: 110
  } ]
  Locallastchangedat;
  @UI.hidden: true
  @EndUserText: { label: 'File', quickInfo: 'File' }
  @UI.identification: [ {
    position: 50 ,
    label: 'File'
  } ]
  @UI.lineItem: [ {
    position: 50 ,
    label: 'File'
  } ]
  @UI.selectionField: [ {
    position: 50
  } ]
  Value;
  @EndUserText.label: 'Mime Type'
  @UI.identification: [ {
    position: 60 ,
    label: 'Mime Type'
  } ]
  @UI.lineItem: [ {
    position: 60 ,
    label: 'Mime Type'
  } ]
  @UI.selectionField: [ {
    position: 60
  } ]
  @UI.hidden: true
  Mimetype;
    
}
```

Update metadata extension for Root Entity
```ABAP
@Metadata.layer: #CORE
@UI.headerInfo.title.type: #STANDARD
@UI.headerInfo.title.value: 'UuidRoot'
@UI.headerInfo.description.type: #STANDARD
@UI.headerInfo.description.value: 'UuidRoot'
annotate view ZC_ZT_ROOT with
{
  @EndUserText.label: 'UuidRoot'
  @UI.facet: [ {
    label: 'General Information',
    id: 'GeneralInfo',
    purpose: #STANDARD,
    position: 10 ,
    type: #IDENTIFICATION_REFERENCE
  },
  { id: 'Child',
        purpose: #STANDARD,
        label: 'Child',
        type: #LINEITEM_REFERENCE,
        position: 20,
        targetElement: '_Child'
      },
      { id: 'PDFAttachments',
        purpose: #STANDARD,
        label: 'PDF Attachments',
        type: #LINEITEM_REFERENCE,
        position: 30,
        targetElement: '_Pdf'
      },
    {
            id: 'ChangeDetails',
            purpose: #STANDARD,
            label: 'Change Log',
            type: #COLLECTION,
            position: 40
       },
       {
          id : 'ChangeInfo',
          label: 'Change Log',
          purpose: #STANDARD,
          parentId : 'ChangeDetails',
          type : #FIELDGROUP_REFERENCE,
          targetQualifier : 'ChangeInformation',
          position: 50
      }
   ]
  @UI.identification: [ {
    position: 10 ,
    label: 'UuidRoot'
  } ]
  @UI.lineItem: [ {
    position: 10 ,
    label: 'UuidRoot'
  } ]
  @UI.selectionField: [ {
    position: 10
  } ]
  UuidRoot;

  @EndUserText.label: 'SemantickeyRoot'
  @UI.identification: [ {
    position: 20 ,
    label: 'SemantickeyRoot'
  } ]
  @UI.lineItem: [ {
    position: 20 ,
    label: 'SemantickeyRoot'
  } ]
  @UI.selectionField: [ {
    position: 20
  } ]
  SemantickeyRoot;

  @EndUserText.label: 'Description'
  @UI.identification: [ {
    position: 30 ,
    label: 'Description'
  } ]
  @UI.lineItem: [ {
    position: 30 ,
    label: 'Description'
  } ]
  @UI.selectionField: [ {
    position: 30
  } ]
  Description;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 10 }]
  @UI.lineItem: [ {
    position: 60
  } ]
  @UI.selectionField: [ {
    position: 60
  } ]
  Createdby;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 20 }]
  @UI.lineItem: [ {
    position: 70
  } ]
  @UI.selectionField: [ {
    position: 70
  } ]
  Createdat;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 30 }]
  @UI.lineItem: [ {
    position: 80
  } ]
  @UI.selectionField: [ {
    position: 80
  } ]
  Changedby;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 40 }]
  @UI.lineItem: [ {
    position: 90
  } ]
  @UI.selectionField: [ {
    position: 90
  } ]
  Lastchangedat;
  @UI.fieldGroup: [{ qualifier: 'ChangeInformation', position: 50 }]
  @UI.lineItem: [ {
    position: 100,
    qualifier: 'ChangeInfo'
  } ]
  @UI.selectionField: [ {
    position: 100
  } ]
  Locallastchangedat;
  @EndUserText.label: 'Valid'
  @UI.identification: [ {
    position: 40 ,
    label: 'Valid'
  } ]
  @UI.lineItem: [ {
    position: 40 ,
    label: 'Valid'
  } ]
  @UI.selectionField: [ {
    position: 40
  } ]
  Valid;
  @EndUserText.label: 'Active'
  @UI.identification: [ {
    position: 50 ,
    label: 'Active'
  } ]
  @UI.lineItem: [ {
    position: 50 ,
    label: 'Active'
  } ]
  @UI.selectionField: [ {
    position: 50
  } ]
  Active;
}
```

### Step 7
Expose PDF Attachment entity in Service Definition
```ABAP
@EndUserText.label: 'Service Binding for Root Entity V2'
define service ZUI_ZT_ROOT_V2 {
  expose ZC_ZT_ROOT     as Root;
  expose ZC_ZT_CHILD    as Child;
  expose ZC_ZT_GCHILD   as GrandChild;
  expose ZC_ZT_GGCHILD  as GreatGrandChild;
  expose ZC_ZT_ROOT_PDF as PdfAttachment;
}
```

### Fiori Preview

![PDF Attachments Creation](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/PDF%20Attachments%20Creation%20Fiori%20Preview.png)

![PDF Attachments Creation](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/PDF%20Attachments%20Creation%20Fiori%20Preview%202.png)

![List Rerport](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/PDF%20Attachments%20Creation%20Fiori%20Preview%203.png)

![PDF Attachments Mimetype](https://github.com/shindechaitanya15/RAP-Developments/blob/main/RAP%20Development%20Images/PDF%20Attachments%20Mimetype.png)

</details>

 _Thank you for reading, I hope this helps you in implementing more such scenarios for specific requirements._
