Redis Client Types trong Node.js
1. createClient - Client đơn lẻ
javascript
import { createClient } from 'redis';

const client = createClient({
  url: 'redis://localhost:6379'
});

await client.connect();
Dùng khi: Kết nối đến một Redis server duy nhất (phổ biến nhất)

2. createCluster - Redis Cluster
javascript
import { createCluster } from 'redis';

const cluster = createCluster({
  rootNodes: [
    { url: 'redis://localhost:30001' },
    { url: 'redis://localhost:30002' },
    { url: 'redis://localhost:30003' }
  ]
});

await cluster.connect();
Dùng khi: Làm việc với Redis Cluster (phân tán dữ liệu tự động)

Đặc điểm:

Dữ liệu được sharding tự động

High availability

Horizontal scaling

3. createSentinel - Sentinel Mode
javascript
import { createSentinel } from 'redis';

const client = createSentinel({
  sentinels: [
    { host: 'sentinel1.example.com', port: 26379 },
    { host: 'sentinel2.example.com', port: 26379 },
    { host: 'sentinel3.example.com', port: 26379 }
  ],
  name: 'mymaster' // Tên master group
});
Dùng khi: Cần high availability với Redis Sentinel

Đặc điểm:

Tự động failover

Service discovery

Monitoring

4. createClientPool - Connection Pool
javascript
import { createClientPool } from 'redis';

const pool = createClientPool({
  url: 'redis://localhost:6379',
  pool: {
    max: 10,  // Số connection tối đa
    min: 2    // Số connection tối thiểu
  }
});

// Sử dụng connection từ pool
const client = await pool.acquire();
try {
  await client.set('key', 'value');
} finally {
  await pool.release(client);
}
Dùng khi: Ứng dụng high-throughput cần nhiều connection

Đặc điểm:

Tránh connection overhead

Quản lý connection hiệu quả

Tối ưu performance

5. So sánh chi tiết
Loại	Use Case	Ưu điểm	Nhược điểm
createClient	App nhỏ, single server	Đơn giản, dễ dùng	Single point of failure
createCluster	Large scale, sharding	Scalability, performance	Phức tạp setup
createSentinel	High availability	Auto failover, reliable	Cần nhiều server
createClientPool	High concurrency	Performance, resource management	Code phức tạp hơn
6. Ví dụ thực tế
Development/Staging (Dùng createClient)
javascript
// Đơn giản cho môi trường dev
const devClient = createClient({
  url: 'redis://localhost:6379'
});
Production với High Availability (Dùng Sentinel)
javascript
const productionClient = createSentinel({
  sentinels: [
    { host: '10.0.1.1', port: 26379 },
    { host: '10.0.1.2', port: 26379 },
    { host: '10.0.1.3', port: 26379 }
  ],
  name: 'my-master'
});
Microservices (Dùng Cluster)
javascript
const microserviceClient = createCluster({
  rootNodes: [
    { url: 'redis://cluster-node-1:6379' },
    { url: 'redis://cluster-node-2:6379' },
    { url: 'redis://cluster-node-3:6379' }
  ]
});
7. Lựa chọn theo nhu cầu
Bắt đầu/Dev: createClient

Production cơ bản: createClient + replication

High traffic: createClientPool

Enterprise scale: createCluster hoặc createSentinel

8. Các constants khác
CLUSTER SLOT_STATES: Trạng thái slots trong cluster

CommandTimeoutDuringMaintenanceError: Lỗi timeout khi cluster bảo trì

Khuyến nghị: Bắt đầu với createClient trước, khi cần scale thì chuyển sang các options khác.