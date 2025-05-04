# Memoir Security

To simplify the permissions model Claims Based Access Controls (CBAC) AKA
Attribute Based Access Contorls (ABAC) will be used.

CBAC provides a mostly easy to manage model and it allows finer grained
permissions to be added at a future date without having to change the security
paradigm.

Permission on a collection is also inherited. If a role is assigned a permission
to a top level collection then any child collections will inherit that permission.

## For Example...

Default Roles: Admin, ContentModerator, Viewer, World
Custom Roles: Adult, Youth

Permissions in the system (If you are in the role):

----------------

- Global Permissions:
  - manage_users: [Admin]
  - all_collections_write: [Admin]
  - all_collections_read: [Admin, Adult]
  
- Collection "Youth Memories" assigned roles:
  - read: [Admin(Inherited), Adult(inherited), Child]
  - write: [Admin(Inherited), Adult(inherited)]

- Collection "Adult Only" assigned roles:
  - read: [Admin(Inherited), Adult(inherited)]
  - write: [Admin(Inherited), Adult(added)]

- Collection "Adult Only View" assigned roles:
  - read: [Admin(Inherited), Adult(inherited)]
  - write: [Admin(Inherited)]

----------------

- Users:

  - User: SallyAnn
  - Role: Admin
  - Result:
    - Full access to the system

  - User: Dad
  - Role: Adult
  - Result:
    - Can't manage users
    - Can only view "Adult Only View" collection
    - Can read/write "Adult Only" collection

  - User: Kid
  - Role: Youth
  - Result:
    - Can't manage users
    - Can't see any  "Adult" collection
    - Can see the "Youth Memories" folder but can't edit