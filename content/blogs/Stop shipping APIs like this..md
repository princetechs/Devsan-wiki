# Stop Shipping Api/action/params : A Practical Guide to RESTful APIs That Scale

Designing APIs isn’t just about making something work—it’s about making it work well at scale, for real teams, in real systems. If an endpoint like /api/leave?workspaceId=123&groupId=456 looks familiar, this post is for you. Let’s turn quick wins into solid, scalable patterns.

## TL;DR

- Prefer resource-based URLs; let HTTP methods express the action.
    
- Model “leave” as removing a membership resource using DELETE.
    
- Start simple if you must—but design so it’s easy to evolve.
    
- Separate domains (workspaces, groups, memberships) when scaling or organizing teams.
    

---

## Part 1: The Problem With /leave

Developers often add a single “do the thing” endpoint:

- POST /api/leave?workspaceId=123&groupId=456
    

Why it’s tempting:

- It’s quick to ship.
    
- It matches a product action (“Leave this group”).
    
- It avoids redesigning existing routes.
    

Why it bites later:

- Ambiguous context: Are we leaving a workspace, a group, or both?
    
- Hard to secure: Who’s allowed to leave whom and where?
    
- Tough to scale: As services split, /leave becomes a cross-domain knot.
    
- Poor caching, unclear contracts, awkward SDKs.
    

---

## Part 2: Reframing “Leave” as a Resource Operation

In REST, URLs name resources (nouns). The HTTP method expresses the action (verb).

- To “leave a workspace,” delete the membership linking user→workspace.
    
- To “leave a group,” delete the membership linking user→group (within workspace context if needed).
    

Endpoints:

- DELETE /api/v1/workspaces/{workspaceId}/members/{userId}
    
- DELETE /api/v1/workspaces/{workspaceId}/groups/{groupId}/members/{userId}
    

Why it works:

- Clear intent: The URL names the relationship being removed.
    
- Better auth: Path reveals scope, roles, and policy boundaries.
    
- Easy to scale: Each resource belongs to a domain; no “god” endpoints.
    
- Predictable behavior: Fewer hidden rules in query params or bodies.
    

---

## Part 3: Step-by-Step Migration Plan

Step 1: Introduce the resource-first routes

- Keep the old /leave endpoint behind a feature flag if needed.
    
- Add new DELETE routes for memberships.
    

Step 2: Update clients in phases

- Frontend: switch calls to the new DELETE routes.
    
- Backend: implement adapters or API gateway rewrites temporarily.
    

Step 3: Move validation and auth to the right place

- Validate membership existence before delete.
    
- Ensure permissions:
    
    - Self-leave: userId must equal authenticated user.
        
    - Admin-remove: requires role/permission within that resource scope.
        

Step 4: Add batch operations if necessary

- DELETE /api/v1/workspaces/{workspaceId}/groups/{groupId}/members  
    Body: { "userIds": ["u1","u2"] }
    

Step 5: Deprecate /leave

- Communicate timelines and upgrade guides.
    
- Emit deprecation warnings in logs and response headers.
    

---

## Part 4: Realistic Patterns You Can Ship

Fetching:

- GET /api/v1/workspaces/{workspaceId}/groups
    
- GET /api/v1/workspaces/{workspaceId}/groups?id={groupId}
    
- GET /api/v1/workspaces/{workspaceId}/groups?status=active&type=public
    

Leaving (remove membership):

- DELETE /api/v1/workspaces/{workspaceId}/members/{userId}
    
- DELETE /api/v1/workspaces/{workspaceId}/groups/{groupId}/members/{userId}
    

Batch removal:

- DELETE /api/v1/workspaces/{workspaceId}/groups/{groupId}/members  
    Body: { "userIds": ["u1","u2","u3"] }
    

“Me” convenience routes (optional):

- POST /api/v1/me/leave-group  
    Body: { "groupId": "grp-456", "workspaceId": "ws-123" }
    

Use “me” for UX convenience; keep core routes resource-based.

---

## Part 5: Microservices-Friendly Design

Domain separation that scales:

- Workspace Service: owns workspaces and workspace memberships.
    
- Group Service: owns groups and group memberships (scoped to workspaces).
    
- Identity/Auth Service: owns users, tokens, roles.
    

Why this structure helps:

- Independent scaling: Group ops might be 10x workspace ops—scale only that service.
    
- Failure isolation: Group service downtime won’t break workspace listing.
    
- Clear data ownership: Each service controls its tables and logic.
    
- Clean contracts: APIs map directly to domain concepts.
    

API Gateway tips:

- Route by path prefix: /workspaces/* → Workspace svc; /groups/* → Group svc.
    
- Consistent error formats across services.
    
- Centralized auth; service-level authorization.
    

---

## Part 6: Common Questions (and Honest Answers)

Q: Is using /leave always wrong?

- A: No. For MVPs and small teams, /leave can be fine. But design with an easy path to refactor. Name your internal methods and domain objects around memberships from day one.
    

Q: Why not use POST /groups/{groupId}/leave for clarity?

- A: You can. It’s pragmatic for “self-leave.” But DELETE on the membership resource is more expressive, cacheable, and consistent. If you use POST for /leave, treat it as a thin facade calling the DELETE internally.
    

Q: What if I need both workspaceId and groupId?

- A: Use the hierarchical path: /workspaces/{workspaceId}/groups/{groupId}/members/{userId}. It disambiguates context and simplifies authorization.
    

Q: How do I handle “current user” without passing userId?

- A: Use a “me” route or infer from the auth token:
    
    - DELETE /api/v1/workspaces/{workspaceId}/members/me
        
    - Your backend maps “me” to the authenticated user.
        

Q: Isn’t splitting services overkill?

- A: It depends on your team size, traffic, and change velocity. Start modular in code (clear boundaries), split into services when you see scale/ownership pain.
    

Q: How should I design filters for fetching groups?

- A: Use query params on list endpoints:
    
    - GET /workspaces/{id}/groups?status=active&type=public
        
    - You can allow multiple IDs: ?id=g1,g2,g3
        

---

## Part 7: A Practical Checklist Before You Ship

- URL names a noun (resource), not a verb (action).
    
- HTTP method expresses the intent (GET/POST/DELETE/PATCH).
    
- Path reveals scope and authorization boundaries.
    
- Filters live in query parameters, not new endpoints.
    
- Batch operations exist for high-traffic workflows.
    
- You can route endpoints cleanly to separate services later.
    
- Deprecation plan exists for any action-style legacy routes.
    

---

## Part 8: Copy-Paste Snippets

Example: Leave group

- Request:  
    DELETE /api/v1/workspaces/ws-123/groups/grp-456/members/user-789
    
- Response:  
    {  
    "success": true,  
    "action": "membership_removed",  
    "workspaceId": "ws-123",  
    "groupId": "grp-456",  
    "userId": "user-789"  
    }
    

Example: Self-leave convenience

- Request:  
    POST /api/v1/me/leave-group  
    {  
    "workspaceId": "ws-123",  
    "groupId": "grp-456"  
    }
    
- Server internally calls the DELETE membership route.
    

---

## Part 9: When to Break the Rules (Consciously)

- Strong UX constraint: “Leave” must be a single-click operation for current user.
    
- Legacy client support: Hard to change app code quickly.
    
- Temporary feature flags: Feature in beta behind an action route before stabilizing the resource model.
    

Break the rule—but document it, wrap it, and plan to migrate.

---

## Conclusion

Resource-first APIs make scaling boring—predictable auth, clean contracts, tidy routes, and easy ownership boundaries. Start where you are (even if that’s /leave), but design like you’ll need to scale tomorrow. Because one day, you will.