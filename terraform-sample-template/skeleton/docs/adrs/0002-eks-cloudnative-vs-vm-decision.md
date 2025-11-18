# ADR 003: Selection of Amazon EKS as the Kubernetes Platform

- **Date:** 2025-11-17  
- **Status:** Accepted  
- **Deciders:** Architecture & DevOps Team  
- **Technical Area:** Platform Engineering / Cloud Infrastructure  

---

## 1. Context

Our application architecture is moving toward a microservices and cloud-native model. To support this shift, we require a robust Kubernetes platform with:

- High availability and reliability  
- Strong security and IAM integration  
- Automated cluster upgrades and lifecycle management  
- Compatibility with GitOps, service mesh, and CI/CD pipelines  
- Support for auto-scaling and modern workloads (e.g., GPU, serverless, spot instances)  
- Enterprise-grade governance and operational capabilities  

We evaluated multiple Kubernetes options including:

- Amazon EKS  
- Self-managed Kubernetes on EC2  
- Other managed services (GKE, AKS)  
- Kubernetes on-prem or hybrid solutions  

---

## 2. Decision

We **will use Amazon Elastic Kubernetes Service (EKS)** as the primary Kubernetes platform for production and non-production environments.

---

## 3. Rationale

### **1. Fully Managed Control Plane**
EKS provides a **highly available**, **managed control plane** with automatic patching and control plane updates.  
This reduces operational burden significantly compared to self-managed Kubernetes.

### **2. Deep AWS Integration**
EKS integrates tightly with:
- IAM for fine-grained role-based access control  
- VPC networking and security groups  
- CloudWatch, X-Ray, and AWS Observability tooling  
- ECR for container image storage  
- Load Balancers (ALB/NLB)  
- KMS for encryption  

These integrations reduce complexity and ensure consistent security policies.

### **3. Standard, Upstream Kubernetes**
EKS runs **upstream CNCF-conformant Kubernetes**, ensuring portability and avoiding vendor-specific lock-in at the Kubernetes API level.

### **4. Autoscaling & Cost Optimization**
EKS supports:
- Cluster Autoscaler  
- Karpenter node provisioning  
- Spot instance integration  
- GPU and high-memory instances  
- Fargate serverless pods (optional)

This provides strong flexibility for cost-control and performance optimization.

### **5. Ecosystem & Enterprise Support**
EKS is widely adopted, well-documented, and supported by:
- Helm charts  
- Operators  
- Service meshes (Istio, Linkerd, App Mesh)  
- GitOps tools (ArgoCD, FluxCD)

This ecosystem reduces friction when integrating new tooling.

### **6. Reliability & Security**
AWS provides:
- Multi-AZ control plane redundancy  
- Automatic encryption with KMS  
- Native security best practices  
- 24/7 enterprise support  

This meets our disaster recovery and uptime requirements.

---

## 4. Alternatives Considered

### **A. Self-Managed Kubernetes on EC2**
- Pros: Full control, customizable  
- Cons: High operational overhead, complex updates, security risk  

### **B. Google Kubernetes Engine (GKE)**
- Pros: Very advanced automation  
- Cons: Locks us into GCP; does not align with current AWS-first cloud strategy  

### **C. Azure Kubernetes Service (AKS)**
- Pros: Good enterprise integration (Microsoft ecosystem)  
- Cons: Not compatible with current AWS infrastructure and tooling  

---

## 5. Consequences

### **Positive**
- Reduced operational burden  
- Strong security posture via IAM + VPC integration  
- High reliability and performance  
- Compatibility with AWS-native architecture  
- Simplifies CI/CD and GitOps workflows  
- Easy autoscaling and cost optimization with spot nodes and Karpenter  

### **Negative / Risks**
- EKS pricing + VPC networking costs  
- AWS dependency (vendor lock-in at infrastructure level)  
- Requires Kubernetes expertise for teams  
- Initial learning curve for developers  

---

## 6. Action Items

1. Provision EKS clusters using Terraform  
2. Deploy Karpenter for node autoscaling  
3. Configure CI/CD pipelines for Kubernetes manifests  
4. Set up observability stack (Prometheus, Grafana, CloudWatch)  
5. Configure IAM roles for service accounts (IRSA)  
6. Deploy cluster add-ons (Ingress, CNI, CSI drivers, metrics server)  
7. Document operational runbooks  

---

## 7. Decision Review

- **Next review:** Q3 2026  
- Review based on platform stability, cost, and potential adoption of multi-cloud or hybrid Kubernetes.

---
