### Creating a replica set in MongoDB using Docker

```create docker network:> docker network create mongoreplsetnetwork```

## เราต้องการสร้าง replica set (3 nodes)create container from image: mongo:6.0 > docker run -d -p 30001:27017 --net mongoreplsetnetwork --name mongo1 mongo:6.0 --replSet localmongoreplSet

> ```docker run -d -p 30002:27017 --net mongoreplsetnetwork --name mongo2 mongo:6.0 --replSet localmongoreplSet```
> ```docker run -d -p 30003:27017 --net mongoreplsetnetwork --name mongo3 mongo:6.0 --replSet localmongoreplSet```

# เราจะให้ mongo1 เป็น PRIMARY NODE
# เราจะไป config mongodb ใน container mongo1

> ```docker exec -it <CONTAINER_ID_MONGO1> bash```

*พอ access เข้ามาใน container แล้ว โดยปกติไฟล์ .conf ของ mongodb จะอยู่ใน path: /etc/mongod.conf.orig*

**เราจะใช้วิธี shell เข้าไปใน mongodb**

> ```mongosh```

### ซึ่งหน้าตาก็จะเหมือนเวลาเรา shell เข้าไปในฐานข้อมูล sql เลย เราจะเริ่ม config mongodb ให้เป็น replica set

> config = {_id: <REPLICA SET NAME>, // string
    members: [
		{ _id: 0, host: <HOST IP:HOST PORT> },
		{ _id: 1, host: <HOST IP:HOST PORT> },
		{ _id: 2, host: <HOST IP:HOST PORT>, arbiterOnly: true }
  ]}
**example:**
config = {
  _id: 'localmongoreplSet',
  members: [
    { _id: 0, host: '192.168.1.8:30001' },
    { _id: 1, host: '192.168.1.8:30002' },
    { _id: 2, host: '192.168.1.8:30003', arbiterOnly: true }
  ]
}

### ซึ่งจากด้านบนมันคือการกำหนดค่า config จากนั้นเราจะยัด config ใส่ใน rs.initiate()

> rs.initiate(config)

> server จะ return ค่า { ok: 1 }

*เท่านี้เราจะก็จะได้ MongoDB ที่เป็น replica set 3 nodes แล้ว*

*สามารถที่จะ connect ผ่าน mongodb compass ได้เลย*
```mongodb://<local ip>:30001,1<local ip>:30002,<local ip>:30003/?replicaSet=localmongoreplSet```

### reference : https://gist.github.com/asoorm/7822cc742831639c93affd734e97ce4f
