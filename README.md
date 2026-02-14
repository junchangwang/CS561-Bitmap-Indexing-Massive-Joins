
### About this project

This project provides a framework to accelerate massive joins in TPC-DS using bitmap indexing in DuckDB. The repository contains TPC-H reference implementations, and a framework that allows students to quickly implement their TPC-DS queries.

### Walk through the code 

- Check the directories containing generated bitmaps. For example, the directory "bmz_15000000_orderkey" contains the zipped bitmap instance for the attribute orderkey with 15 million tuples (SF = 10). Read extension/debit/dbgen/bm_dbgen.cpp to understand how these bitmaps are interpreted.

- The files extension/debit/execution/tpch/query/Q*.cpp contain the bitmap-powered solutions for TPC-H queries implemented by TAs. Please review Q5.cpp specifically to understand how we use bitmap indexes to accelerate binary joins.

- The extension/debit/execution/tpcds/ directory contains the framework for your TPC-DS implementation. Use the provided Q3.cpp as a template when writing other queries (e.g., Q29).

### Run the code

Add the CUBIT-dev submodule (`RABIT` branch) to `extension/debit`:

```sh
cd extension/debit
git submodule add -b RABIT https://github.com/junchangwang/CUBIT-dev.git
git submodule update --init --recursive
```

Build the project and load bitmap indexes into DuckDB：

```sh
make release (or debug)
```

```DuckDB
pragma load_bitmap(col_name1, col_name2);
```

Here `col_name1` and `col_name2` are bitmap-indexed columns involved in the queries. For example, run the following command to load the bitmaps required for TPC-H Q6:

```DuckDB
pragma load_bitmap(shipdate,discount,quantity);
```

Execute the bitmap-powered Q6 with the following command:

```DuckDB
pragma bm_tpch(6);
```

To run the standard query (without bitmap acceleration), use:

```DuckDB
pragma tpch(6);
```

Note that the required bitmaps for standard TPC-H queries are as follows:

```
(shipdate, linestatus, returnflag) for Q1
(orderdate_GE_364) for Q5
(shipdate_GE_364, discount, quantity) for Q6
(shipdate_GE_30) for Q14
```

Similarly, for TPC-DS queries (e.g., Q3), use the following command:

```DuckDB
pragma load_bitmap(ss_item_sk);
pragma bm_tpcds(3);
```

---

Happy coding! If you have any questions, please feel free to contact the TAs.