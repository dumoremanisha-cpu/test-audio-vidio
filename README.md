## 🎬 Project Overview
Build a serverless web application that transforms static scripts with dynamic names and descriptions into professional videos featuring lip-synced talking photo avatars. The platform generates introduction videos (with photo lip-sync) and presentation videos (with slides and avatar with lip-sync in positioned bottom-right), then seamlessly combines them into a final video output. Everything is deployed serverless using AWS Amplify—no code changes needed after setup.

## 🎯 Core Workflow

### Stage 1: Introduction Video Generation
- **Inputs**: 
  - Static script template
  - Dynamic name and description
  - Background image/slide
  - Person's image for lip-syncing
- **Processing**:
  - Merge static script with dynamic name/description to create autoscript
  - Convert autoscript to audio using Amazon Polly
  - Apply lip-sync animation to person's image using AI service
  - Composite lip-synced video with photo image in center 
- **Output**: Introduction video (full screen or with background)

### Stage 2: Presentation Video Generation
- **Inputs**:
  - Presentation script
  - Person's image (same for consistency)
- **Processing**:
  - Convert presentation script to audio using Amazon Polly
  - Apply lip-sync animation to person's image
  - Position avatar in bottom-right corner with transparent background
- **Output**: Presentation video with floating avatar

### Stage 3: Video Composition
- **Inputs**: Introduction video + Presentation video
- **Processing**: Concatenate both videos with smooth transitions
- **Output**: Final combined video ready for use

## 🖥️ Frontend User Interface (No Coding Required)
Users interact with a simple web interface to:
1. Upload or select a person's photo for lip-syncing
2. Enter static script template
3. Enter dynamic name and description fields
4. Select background image for introduction video
5. Enter presentation script
6. Click "Generate Introduction Video" → see preview
7. Click "Generate Presentation Video" → see preview
8. Click "Merge Videos" → receive final output
9. Download the complete video

**Technology**: React/Next.js frontend deployed on AWS Amplify Hosting

## 🏗️ Serverless Architecture

### AWS Services Used:
| Service | Purpose |
|---------|---------|
| **AWS Amplify** | Frontend hosting, CI/CD, authentication |
| **Amazon Polly** | Text-to-speech (autoscript & presentation audio) |
| **Amazon S3** | Storage for images, audio files, videos |
| **AWS Lambda** | Orchestration functions and processing logic |
| **AWS Step Functions** | Multi-stage workflow management |
| **Amazon MediaConvert** | Video encoding, composition, concatenation |
| **Third-Party Lip-Sync API** | AI-powered face animation (D-ID, HeyGen, Synthesia, or custom) |
| **Amazon CloudWatch** | Logging, monitoring, debugging |
| **AWS IAM** | Security, role-based access control |
| **AWS Secrets Manager** | Secure credential storage |

## 📂 Project Repository Structure
```
dumoremanisha-cpu/test-audio-vidio/
├── README.txt                          # Complete deployment guide
├── deployment-guide.md                 # Detailed step-by-step instructions
├── amplify.yml                         # Amplify build configuration
├── backend/
│   ├── functions/
│   │   ├── generateAudio/
│   │   │   ├── index.js               # Polly text-to-speech
│   │   │   └── package.json
│   │   ├── generateLipsyncVideo/
│   │   │   ├── index.js               # Orchestrate lip-sync generation
│   │   │   └── package.json
│   │   ├── mergeVideos/
│   │   │   ├── index.js               # Video concatenation logic
│   │   │   └── package.json
│   │   └── orchestrateWorkflow/
│   │       ├── index.js               # Main workflow orchestrator
│   │       └── package.json
│   ├── workflows/
│   │   └── videoGenerationPipeline.asl.json  # Step Functions definition
│   └── config/
│       ├── polly-config.js
│       ├── s3-config.js
│       └── mediaconvert-config.js
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── ImageUploader.tsx
│   │   │   ├── ScriptForm.tsx
│   │   │   ├── VideoPreview.tsx
│   │   │   ├── GenerationProgress.tsx
│   │   │   └── FinalDownload.tsx
│   │   ├── pages/
│   │   │   ├── index.tsx               # Main application page
│   │   │   └── api/                    # Backend API routes
│   │   ├── services/
│   │   │   ├── videoGenerationService.ts
│   │   │   ├── storageService.ts
│   │   │   └── workflowService.ts
│   │   ├── styles/
│   │   │   └── globals.css
│   │   └── App.tsx
│   ├── public/
│   │   └── images/
│   └── package.json
├── .env.example                        # Environment variables template
├── .gitignore
├── package.json                        # Root dependencies
└── cloudformation/
    └── stack-template.yaml             # Optional: IaC for AWS resources
```

## 🚀 Deployment & Implementation Guide

### Prerequisites
- AWS Account with billing enabled
- Node.js 16+ and npm
- Git
- AWS Amplify CLI: `npm install -g @aws-amplify/cli`

### Step 1: Clone & Setup
```bash
git clone https://github.com/dumoremanisha-cpu/test-audio-vidio.git
cd test-audio-vidio
npm install
```

### Step 2: Configure Amplify
```bash
amplify init
# Answer prompts (environment: dev, editor: preferred IDE)
```

### Step 3: Add AWS Services
```bash
# Add S3 storage for media files
amplify add storage
# Select "Content (Images, audio, video, etc.)"

# Add Lambda functions for backend
amplify add function
# Select "Node.js function"
# Repeat for: generateAudio, generateLipsyncVideo, mergeVideos

# Add API for frontend-backend communication
amplify add api
# Select "REST"
```

### Step 4: Environment Configuration
Create `.env.local` in frontend directory:
```env
REACT_APP_AWS_REGION=us-east-1
REACT_APP_S3_BUCKET=your-bucket-name
REACT_APP_POLLY_ENGINE=neural
REACT_APP_LIPSYNC_API_KEY=your-api-key-here
REACT_APP_LIPSYNC_PROVIDER=d-id  # or heygen, synthesia
```

### Step 5: Deploy Backend
```bash
# Configure Lambda functions with Polly and S3 permissions
amplify update function  # For each function, add inline policies

# Push all backend changes to AWS
amplify push

# Verify deployment
amplify status
```

### Step 6: Build & Deploy Frontend
```bash
# Build frontend
npm run build

# Deploy to Amplify Hosting
amplify publish
# Select "Hosting" when prompted
```

### Step 7: Post-Deployment Verification
- [ ] Access frontend URL from Amplify Console
- [ ] Test image upload to S3
- [ ] Generate test audio with Polly
- [ ] Verify Lambda function execution in CloudWatch Logs
- [ ] Test complete video generation pipeline
- [ ] Check video output in S3
- [ ] Download and verify final video quality

## 📊 Cost Estimation (Monthly)
| Service | Volume | Cost |
|---------|--------|------|
| Polly | 100 videos × 500 chars = 50K chars | $0.20 |
| MediaConvert | 100 videos × 2 min = 200 min | $3.00 |
| Lambda | 100 workflows × 5 invocations = 500 | $0.10 |
| S3 Storage | 100 GB | $2.30 |
| Amplify Hosting | 10 GB/month | $0.15 |
| Lip-Sync API | 100 videos × $0.10 | $10.00 |
| **Total Estimate** | | ~$15.75/month |

## ✅ Features Checklist
- [ ] Frontend deployed on Amplify (serverless, auto-scaling)
- [ ] Image upload and validation
- [ ] Script template with dynamic name/description inputs
- [ ] Polly integration for audio generation
- [ ] Lip-sync video generation (introduction)
- [ ] Lip-sync video generation (presentation with avatar positioning)
- [ ] Video concatenation/merging
- [ ] Video preview in UI
- [ ] Download generated video
- [ ] Error handling and user feedback
- [ ] CloudWatch logging for debugging
- [ ] No code changes required for new videos

## 🔐 Security Best Practices
- Store API keys in AWS Secrets Manager (not in code)
- Enable S3 bucket encryption at rest
- Configure S3 bucket policies to restrict access
- Use IAM roles with least-privilege permissions
- Enable CloudTrail for audit logging
- Implement request validation in Lambda
- Use CORS properly for frontend requests
- Rate limit API endpoints to prevent abuse

## 📞 Troubleshooting Guide

### Common Issues & Solutions
| Issue | Solution |
|-------|----------|
| "Cannot access S3 bucket" | Check IAM role permissions and bucket policies |
| "Polly service unavailable" | Verify AWS region and service quota |
| "Lip-sync API failing" | Check API credentials in Secrets Manager |
| "Lambda timeout" | Increase timeout in Amplify function config |
| "Video quality poor" | Adjust MediaConvert resolution settings |
| "Deployment fails" | Run `amplify status` and check error logs |

## 🎓 Learning Resources
- [AWS Amplify Documentation](https://docs.amplify.aws)
- [Amazon Polly Guide](https://docs.aws.amazon.com/polly/)
- [AWS Lambda Best Practices](https://docs.aws.amazon.com/lambda/latest/dg/)
- [MediaConvert User Guide](https://docs.aws.amazon.com/mediaconvert/)

## 📞 Support
For issues, questions, or feature requests, please:
1. Check CloudWatch Logs for error details
2. Review the Troubleshooting Guide above
3. Open an issue in this repository with logs and error messages

---

**Status**: Draft - Ready for Implementation  
**Target Completion**: [Define timeline]  
**Assigned To**: [Team member]  
**Priority**: High (Core project foundation)
