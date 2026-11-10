# Lens: security

**Finds:** what the artifact exposes, trusts or leaves unprotected: the security characteristic
of ISO/IEC 25010:2023 (confidentiality, integrity, non-repudiation, accountability,
authenticity, resistance), read at the level of requirements and design.

**Applies to:** artifacts that touch personal or financial data, authentication or permissions,
secrets, or input from outside the system. Not a penetration test and not a code scan; for
those, use the dedicated tools and say so.

## Checklist

1. **Data:** what sensitive data is collected, stored, logged, sent or shown, and whether each
   use is needed.
2. **Trust boundaries:** where input from users, other systems or files enters, and what
   validates it before use.
3. **Authentication:** who or what must prove identity, how, and what happens when it fails or
   expires.
4. **Authorization:** who may do each action; checks on the server side; access to other
   users' data (IDs in URLs, shared resources).
5. **Secrets:** where keys and credentials live, who can read them, how they rotate.
6. **Transport and storage:** encryption where the rules or regulations require it.
7. **Abuse cases:** what a misuse of the feature looks like (enumeration, replay, bulk
   download, cost abuse) and what limits it.
8. **Third parties:** what data leaves the system, to whom, and what they may do with it.
9. **Audit:** which actions must be traceable to who did them.
10. **Regulation:** obligations the domain carries (privacy, payments, health) that the artifact
    should name.

## Evidence a finding needs

The quoted flow or data element, and the concrete misuse or exposure it allows.

## Cannot see

Implementation flaws (injection, unsafe deserialization, dependency vulnerabilities); those
need code analysis and testing. Say this in the coverage.

## Common false positives

Controls provided by the platform or a shared component the artifact relies on (check the
architecture rules before flagging); generic advice with no path in this design.
