# BBT3104: Lab6 - Query Optimization


| *Key*                                                               | Value                                                                                                                                                                              |
|---------------|---------------------------------------------------------|
| *Group Name*                                                               | A3 |
| *Semester Duration*                                                 | 19<sup>th</sup> September - 25<sup>th</sup> November 2024                                                                                                                       |

## Chosen JOB query
2b
## Graphical Representation of the QEP
JOB Query: 2b  
Graphical Representation:

Aggregate (MIN)
                               |
                            Sort (title)
                               |
                       Hash Join (t.id = mk.movie_id)
                       /                       \
       Hash Join (mc.movie_id = t.id)      Seq Scan (movie_keyword)
       /                   \
Hash Join (cn.id = mc.company_id)   Seq Scan (keyword, filter: k.keyword)
       /           \
Seq Scan (company_name, filter: cn.country_code)  Seq Scan (movie_companies)

               +--------------------------+
               |        Aggregate         |
               |   min(t.title) AS movie_title   |
               +-------------+------------+
                             |
               +-------------v-------------+
               |       Nested Loop         |
               | Inner Join: mc.movie_id = mk.movie_id |
               +-------------+-------------+
                             |
          +------------------v------------------+
          |       Nested Loop                   |
          | Inner Join: t.id = mk.movie_id      |
          +----------------+--------------------+
                           |
         +-----------------v----------------+  +-----------------+
         |      Nested Loop                 |  | Seq Scan: title |
         | Inner Join: cn.id = mc.company_id|  +-----------------+
         +----------------+-----------------+
                           |
         +-----------------v------------------+
         |   Seq Scan: company_name (cn)     |
         | Filter: country_code = '[nl]'     |
         +-----------------------------------+
## Q-Error Result
The Q-Error result for the given JOB query as a terminal output is:
PS C:\Users\tanui\Documents\BBT3104-Lab6of6-QueryOptimization>


                                                               & c:/Users/tanui/Documents/BBT3104-Lab6of6-QueryOptimization/.venv/Scripts/python.exe c:/Users/tanui/Documents/BBT3104-Lab6of6-QueryOptimization/q-error/cardinality-based-q-error-per-node-using-yaml.py
Results for JOB Query 2b:
('007 in Rio',)
PS C:\Users\tanui\Documents\BBT3104-Lab6of6-QueryOptimization> & c:/Users/tanui/Documents/BBT3104-Lab6of6-QueryOptimization/.venv/Scripts/python.exe c:/Users/tanui/Documents/BBT3104-Lab6of6-QueryOptimization/q-error/cardinality-based-q-error-per-node-using-yaml--for-multiple-queries.py


--------------------------------------------------

Query:

    SELECT MIN(t.title) AS movie_title
    FROM company_name AS cn,
         keyword AS k,
         movie_companies AS mc,
         movie_keyword AS mk,
         title AS t
    WHERE cn.country_code ='[nl]'
      AND k.keyword ='character-name-in-title'
      AND cn.id = mc.company_id
      AND mc.movie_id = t.id
      AND t.id = mk.movie_id
      AND mk.keyword_id = k.id
      AND mc.movie_id = mk.movie_id;


Calculation:
Q-Error = max(Estimated Rows / Actual Rows, Actual Rows / Estimated Rows)


Interpretation:
* Q-error = 1 implies a perfect estimation.
* Q-error > 1 indicates how many times the estimate was off compared to the actual execution.


Results:
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Seq Scan, Actual Rows: 1, Estimated Rows: 1, Q-Error: 1.0       
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Bitmap Heap Scan/Bitmap Index Scan, Actual Rows: 41840, Estimated Rows: 298, Q-Error: 140.40268456375838
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Bitmap Heap Scan, Actual Rows: 41840, Estimated Rows: 298, Q-Error: 140.40268456375838
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop, Actual Rows: 41840, Estimated Rows: 34, Q-Error: 1230.5882352941176
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Index Scan, Actual Rows: 4, Estimated Rows: 5, Q-Error: 1.25
Node: Aggregate/Nested Loop/Nested Loop/Nested Loop, Actual Rows: 148552, Estimated Rows: 182, Q-Error: 816.2197802197802       
Node: Aggregate/Nested Loop/Nested Loop/Index Scan, Actual Rows: 0, Estimated Rows: 1, Q-Error: inf
Node: Aggregate/Nested Loop/Nested Loop, Actual Rows: 5228, Estimated Rows: 2, Q-Error: 2614.0
Node: Aggregate/Nested Loop/Index Scan, Actual Rows: 1, Estimated Rows: 1, Q-Error: 1.0
Node: Aggregate/Nested Loop, Actual Rows: 5228, Estimated Rows: 2, Q-Error: 2614.0
Node: Aggregate, Actual Rows: 1, Estimated Rows: 1, Q-Error: 1.0


While results on the output section as a query_results.yaml file is:
query: |2-

      SELECT MIN(t.title) AS movie_title
      FROM company_name AS cn,
           keyword AS k,
           movie_companies AS mc,
           movie_keyword AS mk,
           title AS t
      WHERE cn.country_code ='[nl]'
        AND k.keyword ='character-name-in-title'
        AND cn.id = mc.company_id
        AND mc.movie_id = t.id
        AND t.id = mk.movie_id
        AND mk.keyword_id = k.id
        AND mc.movie_id = mk.movie_id;
      
calculation: Q-Error = max(Estimated Rows / Actual Rows, Actual Rows / Estimated Rows)
interpretation:
  - Q-error = 1 implies a perfect estimation.
  - Q-error >  1 indicates how many times the estimate was off compared to the actual
    execution.
results:
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Seq Scan
    actual_rows: 1
    estimated_rows: 1
    q_error: 1.0
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Bitmap Heap Scan/Bitmap
      Index Scan
    actual_rows: 41840
    estimated_rows: 298
    q_error: 140.40268456375838
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop/Bitmap Heap Scan
    actual_rows: 41840
    estimated_rows: 298
    q_error: 140.40268456375838
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Nested Loop
    actual_rows: 41840
    estimated_rows: 34
    q_error: 1230.5882352941176
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop/Index Scan
    actual_rows: 4
    estimated_rows: 5
    q_error: 1.25
  - node: Aggregate/Nested Loop/Nested Loop/Nested Loop
    actual_rows: 148552
    estimated_rows: 182
    q_error: 816.2197802197802
  - node: Aggregate/Nested Loop/Nested Loop/Index Scan
    actual_rows: 0
    estimated_rows: 1
    q_error: .inf
  - node: Aggregate/Nested Loop/Nested Loop
    actual_rows: 5228
    estimated_rows: 2
    q_error: 2614.0
  - node: Aggregate/Nested Loop/Index Scan
    actual_rows: 1
    estimated_rows: 1
    q_error: 1.0
  - node: Aggregate/Nested Loop
    actual_rows: 5228
    estimated_rows: 2
    q_error: 2614.0
  - node: Aggregate
    actual_rows: 1
    estimated_rows: 1
    q_error: 1.0