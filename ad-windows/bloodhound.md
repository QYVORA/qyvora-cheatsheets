# BloodHound Cheat Sheet
> Active Directory attack-path graphing · AD/Windows · collectors: `SharpHound` (Windows) / `bloodhound-python` (cross-platform)

---

## Quick start
```bash
# Collect (cross-platform, from an authenticated foothold)
bloodhound-python -u user -p pass -d corp.local -ns 10.0.0.10 -c All

# Or on Windows, from a domain-joined box:
SharpHound.exe -c All
```

## Collection methods (`-c`)
`All` `Default` `DCOnly` `Session` `LoggedOn` `Trusts` `ACL` `Container` `RDP` `ObjectProps` `LocalAdmin` `GPOLocalGroup`

## Ingest & analyze
1. Start Neo4j (`neo4j console` or via Docker).
2. Launch BloodHound GUI, log in, upload the collected `.zip`/JSON files.
3. Use built-in queries (left panel) or write custom Cypher.

## Useful built-in queries
- Find all Domain Admins
- Shortest paths to Domain Admins from Owned users
- Find Kerberoastable users
- Find AS-REP roastable users
- Find computers with unconstrained delegation
- Shortest path from Domain Users to High Value targets

## Custom Cypher examples
```cypher
// All users with a path to Domain Admins
MATCH (u:User),(g:Group {name:'DOMAIN ADMINS@CORP.LOCAL'}), p=shortestPath((u)-[*1..]->(g))
RETURN p

// Kerberoastable accounts
MATCH (u:User {hasspn:true}) RETURN u.name

// Computers with unconstrained delegation
MATCH (c:Computer {unconstraineddelegation:true}) RETURN c.name
```

## Marking "Owned" / High Value
Right-click a node → "Mark User as Owned" after popping a box, to recompute paths from your actual foothold.

## Recipes
```bash
# Session + LoggedOn collection to find where admins are logged in
bloodhound-python -u user -p pass -d corp.local -c Session,LoggedOn

# Zip and prep for ingestion
zip -r bh-collection.zip *.json
```

## Gotchas / OPSEC
- SharpHound execution is loud and often flagged by EDR — cross-platform `bloodhound-python` from a low-priv Linux box is stealthier for LDAP-only collection (no `Session`/`LoggedOn`, which need local admin or SMB access to each host).
- Neo4j default credentials (`neo4j/neo4j`) must be changed on first login.

## See also
- https://bloodhound.readthedocs.io/
- `ad-windows/netexec.md`, `ad-windows/impacket.md`, `qyvora/shaka.md`, `qyvora/sundiata.md`
