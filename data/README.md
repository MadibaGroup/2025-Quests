# Data Collection, Anonymization, and Spearman Correlation

Before detailing the anonymization process, we first want to explain how the data was collected and used in our research. The data comes from the quest system’s website interface, where each quest corresponds to specific on-chain actions and interactions with smart contracts. When a user completes a quest, the loyalty program verifies the user’s on-chain transaction to confirm that all requirements are met. Once verified, the program stores the details in the company’s internal database—including transaction hash, user address, and contract address. This record is then merged with additional off-chain information such as the points awarded for completing the quest and the epoch (a time metric controlled by the loyalty program rather than the blockchain).
We were given SQL access to the raw data and exported it as CSV files (anonymized for public release in our repository). Per the company’s request:
- Any identifying information about the blockchain itself, its applications, or specific date ranges that might reveal the identity of the chain has been removed from these CSV files.
- Instead of raw tables, we provide the “result” of certain SQL commands so readers can replicate our analysis and charts without accessing sensitive or identifying details.

For example, the techniques used to calculate various statistics (e.g., Spearman correlation) are documented in our repository. We omit these technical details from the paper itself since they are straightforward and not novel. By way of illustration:
- RQ 2.1 (Points): We ran an SQL command that counts how many times each quest was completed per epoch, then stored that count alongside the quest’s points allocation. The resulting CSV file includes an anonymized quest name, its completion count per epoch, and its point value.
  
Because the company did not want the names of the applications made public (to avoid revealing the blockchain in question), we gave each quest a generic name. Rather than simply labeling them Quest #1, Quest #2, etc., we used placeholders like “ETH_Transfer_IN” that preserve a sense of what the quest entails without exposing the real application or on-chain data.

## Detailed Breakdown of Data Organization
- RQ 2.1 (Points):
We run an SQL query to count the number of times each quest was completed per epoch, then append its point allocation. This results in a table with four columns:
  - Quest
  - Completion
  - Points
  - Epoch
  
- RQ 2.2 (Difficulty):
 We specify which quests were used and provide their difficulty mapping (Easy, Medium, Hard).

- RQ 2.3 (Cost):
 We specify which quests were used and share the cost of each quest in USD, while anonymizing any references to the company’s native token.

- RQ 2.4 (Thresholds):
 We specify which quests were used and indicate which epochs had threshold requirements added or removed.

- RQ 2.5 (Rewards):
 We specify which quests were used and note when they offered token rewards.

By combining the above with RQ_1_anonymized.csv, all our conclusions are replicable.

**Additional Notes on Daily_Refill_Table_1.csv**

These data are on-chain only and have no off-chain component.
Each row in this CSV represents multiple batched transactions. The “disperse” smart-contract method was used to bundle several individual transactions into one.
We have truncated each transaction hash to the last six characters to prevent identification of the chain. We also removed timestamps, contract addresses, and block numbers at the company’s request.
We did retain the value and fee columns (in anonymized form), which could be used to estimate the number of batched transactions.
In summary, the CSV files are structured to allow reproducibility of the study’s main findings, while complying with the company’s strict requirements to prevent identification of the underlying blockchain, its specific applications, and precise timeframes.


## On Spearman correlation

According to Wikipedia, “Spearman correlation is a nonparametric measure of rank correlation (statistical dependence between the rankings of two variables). It assesses how well the relationship between two variables can be described using a monotonic function.”
Because it is nonparametric, Spearman correlation works well with data that are skewed, do not follow a normal distribution, or have a nonlinear relationship. It is also robust to outliers and extreme values because it relies on the ranks rather than the raw differences in values. Instead of looking at the raw values directly, it looks at the rank of each value, focusing on whether there is a monotonic (consistently increasing or decreasing) pattern.
For example, if we want to know whether higher point rewards correlate with higher completion rates, Spearman correlation can show us if there is a monotonic link, i.e., whether increasing one variable consistently corresponds to an increase (or decrease) in the other. This avoids assumptions about the underlying distribution.
Spearman correlation also supports ordinal variables naturally. In a quest system, Difficulty might be labeled as Easy, Medium, or Hard. To apply Spearman’s method, we assign numeric ranks (for instance, Easy = 1, Medium = 2, Hard = 3). If we prefer that a positive correlation coefficient means “easier tasks have higher completions,” we could reverse those codes (Hard = 1, Medium = 2, Easy = 3). In doing so, a positive Spearman correlation would then indicate that as tasks become easier, we see a corresponding rise in the number of completions.
The correlation score is computed for each research question and for each epoch individually. This approach lets us see if the correlation remains consistently strong or weak across epochs, strengthening the insights we can draw from the data. For example, for cost, we found that correlations ranged from -0.843 to -0.896 across epochs, indicating a very strong and consistent correlation factor. While a more in-depth statistical analysis could have reported an average, we opted to give the lower and upper bounds so we could see at a glance how strong or weak the correlation is and whether it varies by epoch.
Another example is the correlation for points, which varied from -0.115 to -0.870 across epochs. This tells us that the correlation itself is not particularly strong. One reason is that, at later epochs, the quest designers reduced the point reward for easy, low-cost quests to a minimum of 500 points. Nonetheless, those quests remained the most frequently completed, because difficulty and cost showed a stronger relationship with completion rates. 
By reporting the entire range of Spearman correlations over time, rather than just a single average, we illustrate how the correlation changes across epochs, making it clearer that points did not exhibit a strong correlation with completions.

