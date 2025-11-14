# Room: Friends (GraphQL) — ShaktiCTF 2025

## Goal

Access a hidden `secretFlag` field on the User type which is only returned when the field is reached via the `friends` resolver (i.e., when `context.traversedFromFriendChain` is true), while bypassing a query depth limit of 3, disabled introspection, and rate limits.

## Tools Used

- GraphQL query crafting and iteration (curl / GraphiQL / custom scripts)
- Thoughtful root-query selection to change traversal origin

## Summary / Exploit

The GraphQL API contains a social graph of users: Monica(1), Rachel(2), Ross(3), Phoebe(4), Joey(5), Chandler(6). The `secretFlag` is only non-null when the resolver context has `traversedFromFriendChain` set to `true` — that flag is set only when a user is reached by following the `friends` field. A depth limit of 3 prevents reaching Chandler (user 6) from Monica (user 1) because Chandler is four friend-steps away.

Key idea: the depth limit counts from the root operation. You can change the root starting user by making `user(id: "2")` (Rachel) or `user(id: "3")` (Ross) the root of the query. From these starting points Chandler is within three friend hops. Because friends traversal sets `traversedFromFriendChain`, querying Chandler through Rachel/Ross's `friends` chain satisfies the condition and returns the `secretFlag`.

## Notes / Steps

1. Inspect GraphQL schema behavior (introspection is disabled — rely on provided documentation and trial queries).
2. Confirm that:
   - Direct `user(id: "6") { secretFlag }` returns `null` (context flag not set).
   - Querying via `friends` sets `context.traversedFromFriendChain`.
3. Observe the friend graph distances (Manually or by enumerating friends of 1 → etc.). Chandler is 4 hops from Monica.
4. Craft root query starting from Rachel (id `"2"`) or Ross (id `"3"`), then follow `friends` → `friends` ... up to depth 3 to reach Chandler (id `"6"`). Example pattern:

```graphql
query {
  user(id: "2") {
    id
    friends {
      id
      friends {
        id
        friends {
          id
          secretFlag
        }
      }
    }
  }
}
```

5. Submit the query. Because each friends resolver sets traversedFromFriendChain, the final secretFlag for Chandler is revealed.

## Useful commands / reminders

- Use small iterative queries to map friend links without hitting depth or rate limits.
- If rate-limited, space requests or use minimal fields to reduce weight.
- Remember that disabled introspection prevents automatic schema discovery — manual trial-and-error is required.

## Lessons Learned

- Graph traversal protections (depth limits, disabled introspection) can be bypassed by changing the root node or the root operation.
- Security logic tied to resolver context can be leveraged: if a context flag is set only through a certain resolver chain, ensure you can reach the target through that exact chain.
- When access control depends on traversal context, changing the query origin often beats trying to break the control directly.
