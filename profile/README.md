## Inspiration
For Ethereum NFT communities it is the most normal thing in the world to verify the holder status of members via collab.land. collab.land is an integral part of the infrastructure and enables many interesting use-cases like token gated communities or token gated raffles. On the Internet computer, there are so far only specialized solutions that work exclusively with the respective projects and were developed specifically for this purpose. Not every project can and not every project *should* develop its own solution. So what has been missing so far is glue - an easy to use solution for community leaders and members to perform holder verification.

## What it does
glue allows projects on the IC that are built using the EXT or DIP721 standards to add holder verification to their discord server.

## How we built it
We have four components that make up this project:
### server
- exposes an API written in JS using `express.js` 
- authenticates discord users via discords `oauth2` solution and `passport.js`
- establishes sessions using `express-session` and persists them with `connect-mongo`
- stores `users` and `guilds` in a `mongodb` database
### backend canister
- written in motoko
- exposes two methods
    - `storeMessage`: takes a discord `userId` and `guildId` as an argument and stores them in a Trie with the caller principal as a key
    - `retrieveMessage`: takes a principal as an argument and returns associated `userId` and `guildId`
### frontend canister
- written using svelte
- consumer facing UI that allows users to login with their discord account and wallet (plug and stoic supportd) to verify holdership of NFTs
### discord bot
- written in `discord.py`
- server admin interface to configure projects for holder verification on their server and generate their unique verification link that is exposed to users


## Challenges we ran into
The hardest thing was fiddling with Cookies. As the frontend is hosted in an asset canister on the IC, the backend and the frontend cannot live on the same server. Cookies can't really be shared cross domains, thus we call an endpoint on our server from the frontend that exposes the `SameSite=None` and `Secure` attributes for `Set-Cookie` Headers. The `Secure` attribute needs an `https` context, but because we're serving our API using an nginx reverse proxy and the connection inside the server is `http`, the cookie was never set. What made this very hard to debug is that this restriction doesn't occur when serving/accessing from localhost and was first experienced when deploying to production. After narrowing the issue down, we finally found relief in a 6 year old closed GitHub issue and everything works as expected :)

## Accomplishments that we're proud of
- adding value to the overall ecosystem of the IC and providing an infrastructure project
- having a working solution ready by the end of the hackathon
- creating our first project relying on oauth2
## What we learned


## What's next for glue
- input validation 
- test everything thoroughly
- add support for telegram
- add secure verifiable raffle feature 
- explore glue as an open internet service 
- explore how glue can be consumed by other projects and services hosted on the IC

