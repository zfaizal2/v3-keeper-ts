# Optimizations

- Noticed roughly 200k margin accounts were being pulled and checked for liquidations
    - `getAllMarginAccounts` SDK method uses `getProgramAccounts` RPC call, which is an extremely expensive call in terms of performance
    - Most of these accounts have zero available margin and/or zero required margin
    - Opportunity here to only target 'at-risk' accounts
    - OPTIMIZATION: filter out these zero margin accounts on subsequent loops to alleviate `getProgramAccounts` call and reduce overall computation from O(n) -> O(log n). 
- Caching
    - Several reduntant RPC calls for account data being made which contain mostly static data
        - exchange account info `getExchange`
        - market account info `getMarketPda` & `getMarkets`
    - OPTIMIZATION: bring calls outside of recurring margin account check, to save a few RPC calls
- Transaction Submission
    - OPTIMIZATION: minor improvements made to include a market based priority fee, and five retries, to ensure liquidation transaction is included in a block without getting dropped.

## Further Optimizations
- Add a websocket call to identify account movements or new accounts to be included in filtered list. 