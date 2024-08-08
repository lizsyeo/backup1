# Otter Pop

The world's greatest Jekyll theme.

Example: <http://tybenz.com>

![](http://jekyllthemes.org/thumbnails/otter-pop.png)


import pandas as pd

# Define the function to calculate CP1 score
def calculate_cp1_score(group):
    total_projects = group[group['Governance Reporting'] == 1].shape[0]
    failed_projects = group[group['CP1'] == 1].shape[0]
    return failed_projects / total_projects if total_projects > 0 else 0

# Calculate CP1 score for each client code
metrics = file_a.groupby('Client Code').apply(calculate_cp1_score).reset_index(name='CP1 Score')

# Add a row index for pivoting
metrics['Metric'] = 'CP1 Score'

# Pivot the DataFrame
pivoted_metrics = metrics.pivot(index='Metric', columns='Client Code', values='CP1 Score')

# Format the scores as percentages
pivoted_metrics = pivoted_metrics.applymap(lambda x: f'{x:.0%}')

# Reset index to make 'Metric' a regular column
pivoted_metrics = pivoted_metrics.reset_index()

# Display the pivoted metrics
print(pivoted_metrics)
