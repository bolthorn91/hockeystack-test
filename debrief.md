## code quality and readability
There are a lot of improvements to do. For example:
- Some namings of methods and variables like when we filtering after association request:
```
  const meetingsAssociations = Object.fromEntries(meetingsAssociationsResults.map(a => {
    if (a.from) {
      contactsToAssociate.splice(contactsToAssociate.indexOf(a.from.id), 1);
      return [a.from.id, a.to[0].id];
    } else return false;
  }).filter(x => x));
  ```
  The a variable of the map would be more readable as "association", the "x" variable of the filter "association".

- Rename the params received on each process 
```
const processContacts = async (domain, hubId, q)
```
"q" -> "qeue

- We could seggregate the logic in some functions with proper syntax as the code shared with you.
- Instead of using conditional primitives we could use expresions.
```
if (new Date() > expirationDate) await refreshAccessToken(domain, hubId);
```
const hasExpired = new Date() > expirationDate

- We could use one try catch and use Error types in "pullDataFromHubspot" method instead try catch per each
- We have some unused vars like on 
```
await new Promise((resolve, reject) => setTimeout(resolve, 5000 * Math.pow(2, tryCount)));
```
rejest is not being used

## architecture
- Instead of writing everything on the same file we could seggregate each method in different process and files. For example one file per process.
- The Queue logic could be seggregated in other file too
- Same for the script itself "pullDataFromHubspot"

## performance
- The most critical using 2 nested loops to request to hubspot. As the code suggested we could make the batch fetch by one side and then request the associations instead on make a loop async request to associate per page while batch fetching.
- There are some methods that are iterating a big collection like 4 or 5 times just to do some stuff that could be done in 1 iteration
```
 const meetingsAssociations = Object.fromEntries(meetingsAssociationsResults.map(a => {
    if (a.from) {
      contactsToAssociate.splice(contactsToAssociate.indexOf(a.from.id), 1);
      return [a.from.id, a.to[0].id];
    } else return false;
  }).filter(x => x));
```
we can comebine all this iteration methods by one reduce.

- We could create maps to access the data to find records instead of using method like find or indexOf
[contactId]: data
[meetingId]: data

