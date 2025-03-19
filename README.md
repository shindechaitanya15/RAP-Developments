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
  <summary></summary>
</details>

 _Thank you for reading, I hope this helps you in implementing more such scenarios for specific requirements._
