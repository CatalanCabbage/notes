# Languages

## Imperative vs Declarative code
The initial versions of databases like IBM IMS (Information Management System) and CODASYL (Conference on Data systems languages) used Imperative language as compared to Declarative language.  

Eg., to get all sharks from a list of animals:  

```js
//Imperative
function getSharks() {
    let sharks = [];
    for (let i = 0; i < animals.length; i++) {
        if (animals[i].species == 'shark') {
            sharks.push(animals[i]);
        }
    }
    return sharks;
}

//Possible optimizations: Sort the data by species, find start and end etc
```

```sql
--Declarative
SELECT * FROM ANIMALS WHERE SPECIES='shark';
--Possible optimizations: ???
--The language developer/query optimizer takes care of this
```

The imperative language tells the computer to **perform operations in a _particular order_**. However, the computer can never be sure if the result depends on the ordering or not and thus cannot introduce major improvements.    
The declarative language tells the computer **the kind of result we need, not how to get it.** It's upto the database engine to decide order, indices, joins, etc. This enables it to implement performance improvements without breaking any queries and hides all implementation details.  

Declarative code also lends itself to parallelization across cores or machines.  
Imperative code is hard to parallelize, since the order might determine the result we get. Running parts of the code in parallel might affect the result.  