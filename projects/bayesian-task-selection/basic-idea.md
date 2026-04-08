Use Bayesian optimization/gaussian processes to select the best combination of source tasks for a specific target task/s. 

In addition, when we select such combination on small PEFT model. Can we transfer the selection to the full models? If yes, we don't have to repeat the process of selection on larger models and only use small models.

To add some information, we can use [SAR](obsidian://open?vault=knowledge-base&file=wiki%2Fmodel-merging%2Fsubspace-alignment) (or other metrics) to inform the gaussian process.