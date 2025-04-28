<br>

# `# SQL ↔ Pandas VS SQl Cheat Sheet:`

<br>


## Basic Operations

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Show table**    | `SELECT * FROM table`                   | `df` or `print(df)`                    |
| **Select cols**   | `SELECT col1, col2 FROM table`          | `df[['col1', 'col2']]`                 |
| **Filter rows**   | `SELECT * FROM table WHERE condition`   | `df[df['condition']]`                  |
| **Limit rows**    | `SELECT * FROM table LIMIT 5`           | `df.head(5)`                           |

## Aggregations

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Count rows**    | `SELECT COUNT(*) FROM table`            | `len(df)` or `df.shape[0]`             |
| **Group by**      | `SELECT col, COUNT(*) FROM table GROUP BY col` | `df.groupby('col').size()` |
| **Multiple aggs** | `SELECT col, AVG(x), MAX(y) FROM table GROUP BY col` | `df.groupby('col').agg({'x':'mean', 'y':'max'})` |

## Joins & Merging

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Inner join**    | `SELECT * FROM a JOIN b ON a.id = b.id` | `pd.merge(a, b, on='id')`              |
| **Left join**     | `SELECT * FROM a LEFT JOIN b ON a.id = b.id` | `pd.merge(a, b, on='id', how='left')` |
| **Right join**    | `SELECT * FROM a RIGHT JOIN b ON a.id = b.id` | `pd.merge(a, b, on='id', how='right')` |
| **Full join**     | `SELECT * FROM a FULL JOIN b ON a.id = b.id` | `pd.merge(a, b, on='id', how='outer')` |

## Sorting & Ordering

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Sort ascending** | `SELECT * FROM table ORDER BY col`      | `df.sort_values('col')`                |
| **Sort descending**| `SELECT * FROM table ORDER BY col DESC` | `df.sort_values('col', ascending=False)`|
| **Multi-sort**    | `SELECT * FROM table ORDER BY col1, col2 DESC` | `df.sort_values(['col1', 'col2'], ascending=[True, False])` |

## Data Modification

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Add column**    | `ALTER TABLE table ADD COLUMN new_col`  | `df['new_col'] = values`               |
| **Update values** | `UPDATE table SET col = value WHERE condition` | `df.loc[condition, 'col'] = value` |
| **Delete rows**   | `DELETE FROM table WHERE condition`     | `df = df[~condition]`                  |

## Advanced Operations

| Operation         | SQL                                      | Pandas                                  |
|-------------------|-----------------------------------------|-----------------------------------------|
| **Distinct**      | `SELECT DISTINCT col FROM table`        | `df['col'].unique()`                   |
| **Case/Where**    | `SELECT CASE WHEN x>0 THEN 'pos' ELSE 'neg' END FROM table` | `df['new_col'] = np.where(df['x']>0, 'pos', 'neg')` |
| **Window func**   | `SELECT col, RANK() OVER (PARTITION BY grp ORDER BY val)` | `df['rank'] = df.groupby('grp')['val'].rank()` |

## Tips
1. SQL `WHERE` ↔ Pandas boolean indexing
2. SQL `GROUP BY` ↔ Pandas `groupby()`
3. SQL `HAVING` ↔ Pandas filter after `groupby()`
4. SQL `JOIN` ↔ Pandas `merge()`
5. SQL `ORDER BY` ↔ Pandas `sort_values()`
