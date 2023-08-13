---
title: sqlite
date: 2023-8-2
author: wflin
tags:
  - sqlite
  - c
top: false
cover: false
toc: false
mathjax: false
summary: "sqlite 一些整理"
categories:
  - c
---

# 安装 sqlite3

* sqlite3 这个是安装 sqlite3 数据库

* libsqlite3-dev 这个安装 sqlite3 开发的套件（头文件和库等）

  >sudo apt install sqlite3 libsqlite3-dev

# 验证

* 查看版本

  > sqlite3

# 常用指令

* .help 查看帮助

* .show 查看命令提示符的默认设置

* .schema sqlite_master 查看表结构

* .databases 查看数据

* .tables 查看表

* > .header on
  >
  > .mode column
  >
  > .timer on

* 增删改查和 mysql 语句的 sql 一样不再重复

# c 语言连接 sqlite3

* 代码 sqlite_test.c

  ```c
  int main() {
      sqlite3 *db;
      char *error_message = 0;
  
      int result = sqlite3_open("test.db", &db);
      if (result != SQLITE_OK) {
          fprintf(stderr, "Cannot open database: %s\n", sqlite3_errmsg(db));
          return 1;
      }
  
      const char *create_table_sql = "CREATE TABLE IF NOT EXISTS Users (ID INTEGER PRIMARY KEY, Name TEXT, Email TEXT);";
      result = sqlite3_exec(db, create_table_sql, 0, 0, &error_message);
      if (result != SQLITE_OK) {
          fprintf(stderr, "SQL error: %s\n", error_message);
          sqlite3_free(error_message);
      }
  
      const char *insert_data_sql = "INSERT INTO Users (Name, Email) VALUES ('John Doe', 'john@example.com');";
      result = sqlite3_exec(db, insert_data_sql, 0, 0, &error_message);
      if (result != SQLITE_OK) {
          fprintf(stderr, "SQL error: %s\n", error_message);
          sqlite3_free(error_message);
      }
  
      const char *select_data_sql = "SELECT * FROM Users;";
      sqlite3_stmt *stmt;
      result = sqlite3_prepare_v2(db, select_data_sql, -1, &stmt, 0);
      if (result != SQLITE_OK) {
          fprintf(stderr, "SQL error: %s\n", sqlite3_errmsg(db));
          return 1;
      }
  
      while (sqlite3_step(stmt) == SQLITE_ROW) {
          int id = sqlite3_column_int(stmt, 0);
          const char *name = (const char *)sqlite3_column_text(stmt, 1);
          const char *email = (const char *)sqlite3_column_text(stmt, 2);
          printf("ID: %d, Name: %s, Email: %s\n", id, name, email);
      }
  
      sqlite3_finalize(stmt);
      sqlite3_close(db);
  
      return 0;
  }
  ```

* 运行指令

  > gcc sqlite_test.c -o sqlite_test -lsqlite3