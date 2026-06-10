# AWS vs Azure Cost Comparison Report
### Cloud Computing Assignment 3
**Student:** Lawmekz  
**Date:** June 10, 2026  
**Repository:** [azure-aws-cost-comparison](https://github.com/Lawmekz/azure-aws-cost-comparison)

---

## 1. Application Specifications

The following specifications define a small web application used as the baseline for all cost estimates across both platforms.

| Parameter | Specification |
|-----------|--------------|
| Application Type | Small web application (portfolio/blog) |
| vCPUs | 2 |
| RAM | 4–8 GB |
| OS | Linux (Ubuntu) |
| Storage | 30 GB SSD |
| Outbound Data Transfer | 50 GB/month |
| Inter-Zone Data Transfer | 50 GB/month |
| Object/Blob Storage | 5 GB Standard |
| Storage Requests (PUT) | 100/month |
| Storage Requests (GET) | 1,000/month |
| Uptime | 730 hours/month (always-on) |

---

## 2. AWS Cost Estimate (Linux EC2 + S3)

**Calculator Link:** https://calculator.aws/#/estimate?id=975d33286f04ea88e6411c1cc1fc9bca90475486  
**Region:** US East (N. Virginia)  
**Instance:** t3.medium (2 vCPU, 4 GiB RAM) — Linux, On-Demand

| Service | Configuration | Monthly Cost |
|---------|--------------|-------------|
| Amazon EC2 (t3.medium) | 1 instance × 730 hrs, Linux, On-Demand | $30.37 |
| EBS Storage (gp3) | 30 GB | $2.40 |
| Outbound Data Transfer | 50 GB to Internet | $4.50 |
| Intra-Region Transfer | 50 GB between AZs @ $0.01/GB | $0.50 |
| Amazon S3 (Standard) | 5 GB storage, 100 PUT, 1,000 GET | $0.12 |
| **Total** | | **$38.39/month** |

**Annual Estimate:** $460.68

---

## 3. Azure Cost Estimate (Virtual Machines + Blob Storage)

**Calculator Link:** https://azure.com/e/44d21622acc44d4794898bf7410721bc  
**Region:** East US  
**Instance:** B2ms (2 vCPU, 8 GiB RAM)

### Scenario A — Linux (Ubuntu)

| Service | Configuration | Monthly Cost |
|---------|--------------|-------------|
| Azure VM (B2ms) | 1 instance × 730 hrs, Linux, Pay-as-you-go | $60.74 |
| Managed Disk (Standard SSD E4) | 32 GiB, LRS | $2.40 |
| Outbound Bandwidth | 50 GB Internet Egress | $0.00 |
| Blob Storage (Hot, LRS) | 30 GB + operations | $1.67 |
| **Total** | | **$64.80/month** |

**Annual Estimate:** $777.60

### Scenario B — Windows with Azure Hybrid Benefit

| Service | Configuration | Monthly Cost |
|---------|--------------|-------------|
| Azure VM (B2ms) | 1 instance × 730 hrs, Windows | $60.74 |
| Windows License (Hybrid Benefit) | Existing license applied | $0.00 |
| Managed Disk (Standard SSD E4) | 32 GiB, LRS | $2.40 |
| Outbound Bandwidth | 50 GB Internet Egress | $0.00 |
| Blob Storage (Hot, LRS) | 30 GB + operations | $1.67 |
| **Total** | | **$64.80/month** |

> **Note:** Azure Hybrid Benefit eliminates the Windows Server license cost entirely, making the Windows VM the same price as Linux. Without Hybrid Benefit, the Windows VM would cost approximately $114/month due to the included license fee (~$49/month extra).

---

## 4. Cost Comparison Summary

| Provider | Scenario | Monthly Cost | Annual Cost |
|----------|----------|-------------|------------|
| **AWS** | Linux EC2 (t3.medium) + S3 | **$38.39** | $460.68 |
| **Azure** | Linux VM (B2ms) + Blob Storage | $64.80 | $777.60 |
| **Azure** | Windows VM + Hybrid Benefit | $64.80 | $777.60 |
| **Azure** | Windows VM (no Hybrid Benefit) | ~$114.00 | ~$1,368.00 |

**AWS is more cost-effective by $26.41/month ($316.92/year)** for a Linux-based small web application on equivalent compute specs.

Key reasons for the difference:
- AWS t3.medium ($0.0416/hr) is cheaper than Azure B2ms ($0.083/hr) for Linux
- AWS provides 100 GB free egress/month; Azure provides unlimited free egress on its global network (first 5 GB then free tier varies)
- Azure's managed disk pricing is similar to AWS EBS for equivalent sizes

---

## 5. Networking Cost Comparison (Task 4)

| Transfer Type | AWS Rate | Azure Rate |
|--------------|----------|------------|
| Inbound from Internet | Free | Free |
| Outbound to Internet (first 100 GB) | $0.09/GB | Free (via Microsoft Global Network) |
| Inter-Zone (within region) | $0.01/GB each way | ~$0.01/GB |
| Inter-Region | $0.02/GB | $0.02–$0.08/GB (varies) |

**Monthly inter-zone cost (50 GB scenario):**
- AWS: 50 GB × $0.01 = **$0.50/month**
- Azure: 50 GB × $0.01 = **~$0.50/month**

**Monthly outbound internet cost (50 GB scenario):**
- AWS: 50 GB × $0.09 = **$4.50/month** (after 100 GB free tier = $0 for under 100 GB)
- Azure: **$0.00** (free via Microsoft Global Network for first 5 GB then tiered — shown as $0 in our estimate)

> Azure has a significant egress advantage for internet-facing workloads routing through its global backbone network.

---

## 6. Discount Mechanisms (Task 5)

### AWS Discount Options

| Option | Discount | Best For |
|--------|----------|---------|
| On-Demand | 0% | Short-term, unpredictable workloads |
| Compute Savings Plan (1-yr, No Upfront) | Up to 66% | Flexible, cross-instance savings |
| EC2 Instance Savings Plan (1-yr) | Up to 72% | Single instance family commitment |
| Reserved Instances (3-yr, All Upfront) | Up to 72% | Stable, long-running workloads |
| Spot Instances | Up to 90% | Fault-tolerant, interruptible workloads |

**AWS t3.medium with Savings Plans (730 hrs/month):**
- On-Demand: $30.37/month
- Compute Savings Plan (3-yr, No Upfront): **$15.04/month** (~50% savings)
- EC2 Instance Savings Plan (3-yr, No Upfront): **$13.14/month** (~57% savings)

### Azure Discount Options

| Option | Discount | Best For |
|--------|----------|---------|
| Pay-as-you-go | 0% | Short-term, unpredictable workloads |
| 1-Year Savings Plan | ~33% | Flexible compute commitment |
| 3-Year Savings Plan | ~55% | Long-term flexible commitment |
| 1-Year Reserved | ~42% | Predictable workloads, single region |
| Azure Hybrid Benefit | Up to 49% | Organizations with existing Windows/SQL licenses |
| Dev/Test Pricing | Varies | Non-production workloads |

**Azure B2ms with savings options (730 hrs/month):**
- Pay-as-you-go: $60.74/month
- 1-Year Savings Plan (~33%): **~$40.70/month**
- 3-Year Savings Plan (~55%): **~$27.33/month**
- 1-Year Reserved (~42%): **~$35.23/month**

---

## 7. Cost-Optimization Strategies

### Strategy 1: Commit to Reserved Instances or Savings Plans
For a small web application running 24/7, **On-Demand pricing is the most expensive option**. Committing to a 1-year Reserved Instance or Savings Plan delivers significant savings with minimal risk:
- AWS: Switch from On-Demand ($30.37) to a 1-Year EC2 Savings Plan (~$19/month) — saving ~$135/year
- Azure: Switch from Pay-as-you-go ($60.74) to a 1-Year Reserved (~$35.23/month) — saving ~$306/year

Both platforms allow exchange or modification of reservations, reducing lock-in risk for a small workload.

### Strategy 2: Right-size the Instance
The B2ms (8 GB RAM) and t3.medium (4 GB RAM) may be over-provisioned for a simple web application. Monitoring actual CPU and memory usage for 2–4 weeks and downsizing if utilization is consistently below 20% can yield significant savings:
- AWS: Downgrade from t3.medium ($30.37) to t3.small ($15.18) — saving ~$183/year
- Azure: Downgrade from B2ms ($60.74) to B1ms ($15.19) — saving ~$547/year

---

## 8. Conclusion

For a **Linux-based small web application**, **AWS is the more cost-effective choice** at **$38.39/month** compared to Azure's $64.80/month — a **40% cost difference**.

However, for a **Microsoft-centric enterprise** with existing Windows Server licenses, **Azure becomes highly competitive** through Azure Hybrid Benefit, eliminating the Windows license cost entirely, and offering deeper integration with Active Directory, Microsoft 365, and existing enterprise agreements.

The right choice depends on the organization's existing infrastructure, licensing agreements, and technical ecosystem — not just raw compute pricing.
