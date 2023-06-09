# CREDIT CARD SPENDING HABITS IN INDIA

**Creator**: Tanuj Shankarwar
**Email**: tshankarwar@ucsd.edu
**Linkedin**: https://www.linkedin.com/in/tanuj-s-662785166/

### First things first, lemme show you what data do we have 

````sql
SELECT *
FROM credit
LIMIT 5
````

**Results**
| Index | City                 | Date       | Card Type | Exp Type | Gender | Value  |
|-------|----------------------|------------|-----------|----------|--------|--------|
| 0     | Delhi, India         | 29-Oct-14  | Gold      | Bills    | F      | 82475  |
| 1     | Greater Mumbai, India| 22-Aug-14  | Platinum  | Bills   
| F      | 32555  |
| 2     | Bengaluru, India     | 27-Aug-14  | Silver    | Bills    | F      | 101738 |
| 3     | Greater Mumbai, India| 12-Apr-14  | Signature | Bills    | F      | 123424 |
| 4     | Bengaluru, India     | 05-May-15  | Gold      | Bills    | F      | 171574 |

### 1. Summary statistics of categorical variables 


| City      | Card Type | Exp Type | Gender | Month |
|-----------|-----------|----------|--------|-------|
| count     | 26052     | 26052    | 26052  | 26052 |
| unique    | 986       | 4        | 6      | 12    |
| top       | Bengaluru | Silver   | Food   | Jan   |
| freq      | 3552      | 6840     | 5463   | 13680 |


We get to know:
- Bengaluru is most frequent in transactions.
- Silver is the most used card Category type.
- Most of the transactions are done in food Category.
- Female Does the most no of transactions.





