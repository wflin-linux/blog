```txt
//这个指令创建和使用数据库，没有数据不会持久化到磁盘中
use articledb

//显示数据库
show dbs

// 显示数据库
db

//删除当前数据库
db.dropDatabase()




//集合的显示创建（了解）

db.createCollection("my")


//集合隐式创建


//删除集合
db.my.drop()

//显示集合
show collections

//插入（单个插入）
db.comment.insert({
    "article_id": "10000",
    "content": "今天天气真好，阳光明媚",
    "user_id": "1001",
    "nick_name": "Rose",
    "create_time": new Date(),
    "like_num": Number(10),
    "state": null})
		
		db.commentplus.insert({
    "article_id": "10000",
    "content": "今天天气真好，阳光明媚",
    "user_id": "1001",
    "nick_name": "Rose",
    "create_time": new Date(),
    "like_num": Number(10),
    "state": null})
//观察上面两个

//批量插入
db.comment.insertMany(
    [
        {
            "article_id": "10001",
            "content": "今天天气真好，阳光明媚",
            "user_id": "1001",
            "nick_name": "Rose",
            "create_time": new Date(),
            "like_num": Number(10),
            "state": null
        },
        {
            "article_id": "10002",
            "content": "今天天气真好，阳光明媚",
            "user_id": "1001",
            "nick_name": "Rose",
            "create_time": new Date(),
            "like_num": Number(10),
            "state": null
        },
        {
            "article_id": "10003",
            "content": "今天天气真好，阳光明媚",
            "user_id": "1001",
            "nick_name": "Rose",
            "create_time": new Date(),
            "like_num": Number(10),
            "state": null
        }
    ]
)

//单个查询
db.comment.find({article_id:"10000"})

//查询所有
db.comment.find()

//查询一个没有的
db.comment.find({article_id:"10005"})

// 只是返回第一条（有多条）
db.comment.findOne({article_id:"10000"})


//只是查询一些属性(不是全属性)
db.comment.find({article_id:"10000"},{article_id:1})
// 对比这两句的区别
db.comment.find({article_id:"10000"},{article_id:1,_id:0})

//批量插入时某条数据失败了（try catch）

// 1. 先删除 comment,方便对比
db.comment.drop()
db.comment.find()

//2.
try{
    db.comment.insertMany(
        [
            {
                "_id": 1,
                "article_id": "10001",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            },
            {
                "_id": 1,
                "article_id": "10002",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            },
            {
                "_id": 1,
                "article_id": "10003",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            }
        ]
    )
}catch(e) {
    print(e)
}
// 报错，只会插入一条


// 文档更新（数据准备）
try{
    db.comment.insertMany(
        [
            {
                "_id": 1,
                "article_id": "10001",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            },
            {
                "_id": 2,
                "article_id": "10002",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            },
            {
                "_id": 3,
                "article_id": "10003",
                "content": "今天天气真好，阳光明媚",
                "user_id": "1001",
                "nick_name": "Rose",
                "create_time": new Date(),
                "like_num": Number(10),
                "state": null
            }
        ]
    )
}catch(e) {
    print(e)
}

//更新
db.comment.update({_id:"1"},{like_num:NumberInt(1001)})

db.comment.update({_id:"1"},{likenum:NumberInt(1001)})

```

