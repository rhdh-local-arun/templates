# ADR 002: Use Object Storage Instead of Block Storage

- **Date:** 2025-11-17  
- **Status:** Accepted  
- **Deciders:** Architecture Team  
- **Technical Area:** Storage Architecture  

---

## 1. Context

The platform needs a highly durable, scalable, and cost-efficient storage solution for:

- Application assets (images, PDFs, videos)  
- Data analytics exports  
- Machine learning model artifacts  
- System backups and logs  
- Large immutable files and static content  

We evaluated **block storage** (EBS, PersistentVolumes, disks) and **object storage** (S3, GCS, Azure Blob, MinIO) to determine the best fit for these requirements.

Key factors considered:

- Cost and scalability  
- Access patterns (sequential, random, streaming)  
- Data growth projections  
- Availability and multi-region durability  
- Integration with cloud-native workloads  
- Performance needs  
- Data immutability and lifecycle policies  

---

## 2. Decision

We will **use object storage as the primary storage solution** for large, static, or infrequently modified data.  
Block storage will continue to be used only for transactional databases and read/write-intensive components.

---

## 3. Rationale

### **Why Object Storage?**

1. **Massive scalability & elasticity**  
   Object storage scales essentially without limits. Unlike block storage—which must be provisioned per disk—object storage automatically grows with demand.

2. **Significantly lower cost**  
   Object storage provides cheaper per-GB pricing and supports lifecycle rules (move to cold storage tiers or archival automatically), reducing long-term storage cost.

3. **High durability and multi-region replication**  
   Cloud object storage typically offers **11+ 9s durability**, automatic replication, and optional cross-region setup—features that require complex manual setups on block storage.

4. **Cloud-native integration**  
   Kubernetes, serverless workloads, data pipelines, and analytics frameworks natively support S3/GCS/Blob URIs, making object storage first-class for cloud-native applications.

5. **Ideal for unstructured and immutable data**  
   Large files, logs, and binary assets do not require low-latency, block-level updates. Object storage is optimized for such usage patterns.

6. **Simplified operations**  
   No need to manage disks, volume resizing, snapshots, or file system corruption risks.  
   Object storage APIs are simple (PUT/GET/DELETE), and access can be controlled via IAM policies.

### **Why Not Block Storage?**

- Requires pre-allocated fixed-size volumes  
- Not cost-effective for large or growing datasets  
- Harder to share across multiple services or clusters  
- Lacks built-in versioning, lifecycle management, or object metadata  
- Requires managing filesystems and mount points  
- Scaling requires downtime or complex orchestration  

Block storage remains ideal for databases, low-latency file operations, and workloads needing random read/write access—not for large static assets.

---

## 4. Alternatives Considered

### **A. Block Storage (EBS, Persistent Disk, Azure Disk)**
- Pros: Low-latency, performant, great for transactional workloads  
- Cons: Volume-based, expensive at scale, no simple replication, not shareable across distributed apps  

### **B. Network File Storage (EFS, Filestore, Azur**