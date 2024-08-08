# Otter Pop

The world's greatest Jekyll theme.

Example: <http://tybenz.com>

![](http://jekyllthemes.org/thumbnails/otter-pop.png)


import pandas as pd

def classify_ppm_ids(file_a, file_d):
    # Add a 'Type' column to file_a
    file_a['Type'] = 'Project'

    # Classify 'Parent' and count children
    parent_ids = file_d['Parent ID'].unique()
    child_ids = file_d['Child ID'].unique()

    file_a.loc[file_a['PPM ID'].isin(parent_ids), 'Type'] = 'Parent'
    file_a.loc[file_a['PPM ID'].isin(child_ids), 'Type'] = 'Child'
    file_a.loc[(file_a['PPM ID'].str.startswith('IDE')) & (file_a['Current Work Status'] == 'Approved'), 'Type'] = 'Prioritized Idea'

    return file_a

file_a = classify_ppm_ids(file_a, file_d)


child_count = file_d.groupby('Parent ID').size().reset_index(name='No of Children')
file_a = file_a.merge(child_count, left_on='PPM ID', right_on='Parent ID', how='left').drop('Parent ID', axis=1)
file_a['No of Children'] = file_a['No of Children'].fillna(0)



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
