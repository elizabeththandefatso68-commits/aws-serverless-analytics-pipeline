# AWS Serverless Analytics Pipeline

> Real-time serverless data pipeline demonstrating AWS best practices using Lambda, API Gateway, S3, and DynamoDB.

[![AWS](https://img.shields.io/badge/AWS-Lambda%20%7C%20S3%20%7C%20DynamoDB-orange)](https://aws.amazon.com)
[![Terraform](https://img.shields.io/badge/IaC-Terraform-623CE4)](https://www.terraform.io/)
[![Python](https://img.shields.io/badge/Python-3.11-blue)](https://www.python.org/)

---

## 📋 Table of Contents
- [Architecture](#architecture)
- [Features](#features)
- [Prerequisites](#prerequisites)
- [Quick Start](#quick-start)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [AWS Services](#aws-services-used)
- [Cost Estimation](#cost-estimation)
- [Monitoring](#monitoring)
- [Cleanup](#cleanup)
- [Author](#author)

---

## 🏗️ Architecture



┌─────────┐ ┌──────────────┐ ┌────────┐ ┌─────────────┐ ┌──────────┐ │ Client │─────▶│ API Gateway │─────▶│ Lambda │─────▶│ S3 │─────▶│ Lambda │ │ │ │ (POST /ingest)│ │ Ingest │ │ (Raw Data) │ │ Process │ └─────────┘ └──────────────┘ └────────┘ └────────┘ └─────┬────┘ │ ┌─────────┐ ┌──────────────┐ ┌────────┐ ┌──────────────┐ │ │ Client │◀─────│ API Gateway │◀─────│ Lambda │◀─────│ DynamoDB │◀─────┘ │ │ │ (GET /events) │ │ Query │ │ (Processed) │ └─────────┘ └──────────────┘ └────────┘ └──────────────┘


### Data Flow
1. **Ingest**: Client sends JSON → API Gateway → Lambda validates → S3 stores raw data
2. **Process**: S3 event triggers Lambda → Transforms data → Stores in DynamoDB
3. **Query**: Client requests → API Gateway → Lambda queries DynamoDB → Returns results

---

## ✨ Features

- ✅ **Event-driven serverless architecture** - No servers to manage
- ✅ **Infrastructure as Code** - Fully automated deployment with Terraform
- ✅ **Cost-optimized** - Pay only for what you use
- ✅ **Scalable** - Automatically handles traffic spikes
- ✅ **Highly available** - Multi-AZ deployment by default
- ✅ **Monitored** - CloudWatch logs and metrics included
- ✅ **Secure** - IAM least-privilege permissions

---

## 📦 Prerequisites

Before deploying, ensure you have:

- **AWS Account** with appropriate permissions
- **AWS CLI** installed and configured ([Install Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html))
- **Terraform** >= 1.0 ([Download](https://www.terraform.io/downloads))
- **Git** for version control

### Configure AWS CLI

```bash



aws configure

Enter your AWS Access Key ID
Enter your AWS Secret Access Key
Default region: us-east-1
Default output format: json

---

## 🚀 Quick Start

### 1. Clone the Repository

```bash



git clone https://github.com/elizabeththandefatso68-commits/aws-serverless-analytics-pipeline.git  cd aws-serverless-analytics-pipeline


### 2. Deploy Infrastructure

```bash



cd terraform terraform init terraform plan terraform apply


Type `yes` when prompted. Deployment takes ~2-3 minutes.

### 3. Note Your API Endpoints

After deployment, Terraform will output:




Outputs: ingest_url = "" query_url = ""


**Save these URLs** - you'll need them for testing!

---

## 💡 Usage

### Send Data (Ingest)

```bash



curl -X POST https://YOUR_API_ENDPOINT/ingest
-H "Content-Type: application/json"
-d '{ "event_type": "page_view", "user_id": "user123", "page": "/home", "timestamp": "2024-01-15T10:30:00Z" }'


**Response:**
```json



{ "message": "Data ingested successfully", "event_id": "abc123-def456-ghi789" }


### Query Events

```bash



curl https://YOUR_API_ENDPOINT/events?limit=10


**Response:**
```json



{ "count": 10, "items": [ { "event_id": "abc123-def456-ghi789", "event_type": "page_view", "timestamp": "2024-01-15T10:30:00Z", "processed_at": "2024-01-15T10:30:02Z" } ] }


### Test with Multiple Events

```bash



Send 5 test events
for i in {1..5}; do curl -X POST https://YOUR_API_ENDPOINT/ingest
-H "Content-Type: application/json"
-d "{"event_type": "test", "user_id": "user$i", "data": "sample$i"}" echo "" done

Wait 3 seconds for processing
sleep 3

Query results
curl https://YOUR_API_ENDPOINT/events?limit=10


---

## 📁 Project Structure




aws-serverless-analytics-pipeline/ ├── README.md # This file ├── .gitignore # Git ignore rules ├── lambda/ # Lambda function code │ ├── ingest/ │ │ └── handler.py # Data ingestion logic │ ├── process/ │ │ └── handler.py # Data processing logic │ ├── query/ │ │ └── handler.py # Query logic │ └── requirements.txt # Python dependencies ├── terraform/ # Infrastructure as Code │ ├── main.tf # Main infrastructure definitions │ ├── variables.tf # Configuration variables │ └── outputs.tf # Output values ├── tests/ # Unit tests (optional) └── docs/ # Additional documentation └── architecture.md


---

## ☁️ AWS Services Used

| Service | Purpose | Why This Service |
|---------|---------|------------------|
| **API Gateway** | HTTP endpoints | Serverless REST API with built-in scaling |
| **Lambda** | Compute | Event-driven, pay-per-execution |
| **S3** | Data lake | Durable, cost-effective storage |
| **DynamoDB** | NoSQL database | Single-digit millisecond latency |
| **CloudWatch** | Monitoring | Logs, metrics, and alarms |
| **IAM** | Security | Least-privilege access control |

---

## 💰 Cost Estimation

### Free Tier (First 12 months)
- **Lambda**: 1M requests/month free
- **API Gateway**: 1M requests/month free
- **DynamoDB**: 25GB storage + 25 WCU/RCU free
- **S3**: 5GB storage free

### Beyond Free Tier (Light Usage)
- **Estimated**: $0-5/month for ~10K requests/month
- **Lambda**: $0.20 per 1M requests
- **API Gateway**: $1.00 per 1M requests
- **DynamoDB**: Pay-per-request pricing
- **S3**: $0.023 per GB/month

### Cost Optimization Features
✅ Pay-per-request billing (no idle costs)  
✅ No provisioned capacity  
✅ Automatic scaling  
✅ S3 lifecycle policies (can be added)

---

## 📊 Monitoring

### View Logs in CloudWatch

```bash



List log groups
aws logs describe-log-groups --log-group-name-prefix /aws/lambda/serverless-pipeline

Tail ingest function logs
aws logs tail /aws/lambda/serverless-pipeline-ingest --follow


### Check Metrics

1. Go to **AWS Console → CloudWatch → Dashboards**
2. View metrics for:
   - Lambda invocations
   - API Gateway requests
   - Error rates
   - Latency (p50, p99)

### Set Up Alarms (Optional)

```bash



Example: Alert on Lambda errors
aws cloudwatch put-metric-alarm
--alarm-name lambda-errors
--metric-name Errors
--namespace AWS/Lambda
--statistic Sum
--period 300
--threshold 5
--comparison-operator GreaterThanThreshold


---

## 🧹 Cleanup

**Important:** Destroy resources to avoid charges!

```bash



cd terraform terraform destroy


Type `yes` when prompted.

This will delete:
- All Lambda functions
- API Gateway
- S3 bucket (must be empty)
- DynamoDB table
- IAM roles and policies

---

## 🎯 Key Takeaways

This project demonstrates:

1. **Serverless Architecture** - No EC2 instances to manage
2. **Event-Driven Design** - S3 triggers Lambda automatically
3. **Infrastructure as Code** - Reproducible deployments
4. **AWS Best Practices** - Security, monitoring, cost optimization
5. **Real-World Use Case** - Log processing, analytics, IoT data ingestion

---

## 🔧 Troubleshooting

### Issue: `terraform apply` fails with "bucket already exists"

**Solution:** S3 bucket names must be globally unique. Change `project_name` in `terraform/variables.tf`:

```hcl



variable "project_name" { default = "serverless-pipeline-YOUR_INITIALS" # Add your initials }


### Issue: API returns 500 error

**Solution:** Check Lambda logs:

```bash



aws logs tail /aws/lambda/serverless-pipeline-ingest --follow


### Issue: Events not appearing in DynamoDB

**Solution:** 
1. Check S3 bucket has files in `raw/` prefix
2. Check Process Lambda logs for errors
3. Verify S3 event notification is configured

---

## 🚀 Future Enhancements

- [ ] Add API authentication (AWS Cognito)
- [ ] Implement CloudWatch dashboard (Terraform)
- [ ] Add SNS email alerts for errors
- [ ] Create frontend visualization (React + S3)
- [ ] Add data validation with JSON Schema
- [ ] Implement S3 lifecycle policies
- [ ] Add CI/CD with GitHub Actions
- [ ] Add unit tests with pytest

---

## 👨‍💻 Author

**Elizabeth Molepo**  
Cloud Support Engineer | AWS Certified Cloud Engineer

- 🔗 [LinkedIn](https://www.linkedin.com/in/elizabeth-molepo-141240182)
- 📧 [Email](mailto:elizabeththandefatso68@gmail.com.com)

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- AWS Documentation
- Terraform AWS Provider
- AWS Community Builders

---

## ⭐ Support

If you found this project helpful, please give it a ⭐ on GitHub!

