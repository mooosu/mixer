# Mixer

Mixer is not only a mysql proxy, its aim to supply a simple solution for mysql use.

Mixer contains three key modules:

- mysql, provides a generic interface for mysql use like golang database/sql.
- parser, builds mysql query ast, now only supports lexer.
- proxy, an agent between mysql client and server, like mysql-proxy, atlas, cobar, etc...

## mysql

mysql provieds a simple interface for mysql use, very like golang database/sql but has a little different.

    //create a db, with max 16 idle connections
    //dsn format is <username>:<password>@<host>:<port>/<database>
    db := NewDB("qing:admin@127.0.0.1:3306/mixer", 16)

    //ping remote mysql server is alive
    db.Ping()

    //exec a query, return Result
    r, err := db.Exec("insert into mixer_conn (id, str) values (1, `abc`)")
    println(r.LastInsertId(), r.RowsAffected())

    //exec a query with placeholds, return Result
    r, err := db.Exec("insert into mixer_conn (id, str) values (?, ?)", 2, "efg")
    println(r.LastInsertId(), r.RowsAffected())

    //query a query, return Resultset
    r, err := db.Query("select str from mixer_conn where id = 1")
    str, _ = r.GetString(0, 0)
    str, _ = r.GetStringByName(0, "str")

    //begin a transaction
    tx, err = db.Begin()

    //tx exec a query
    tx.Exec("insert into mixer_conn (id, str) values (3, `abc`)")

    //tx commit
    tx.Commit()

    //prepare statement
    s, err := db.Prepare("insert into mixer_conn (id, str) values(?, ?)")
    
    //exec statement
    s.Exec(5, "abc")
    
    //close statement
    s.Close()

    //get a conn for special use, like set charset
    conn, err = db.GetConn()

    conn.SetCharset("gb2312")

## parser

now only a simple lexer provided, later I will add syntax analyzer to build query AST.

parser's another goal is to parse mysql query and to prevent mysql injection attacks, todo later.

## proxy

proxy aims to solve some mysql use problem in my company, which expects to support following feature:

- split read and write.
- mysql node HA, if main node crashed, switch to backup node automatically.
- supply custom rule to dispatch query to different mysql nodes. [Not Supported Now]
- statistics. [Not Supported Now]

# Feedback

now mixer is only simple and not perfect. I need your feedback to improve continually. Thank you very much!

Email: siddontang@gmail.com

QQ: 335498184

