
# Backend Server Structure (Node.js + Express + MongoDB)

This is the complete backend structure you would need to deploy separately:

## 1. Package.json
```json
{
  "name": "resume-builder-backend",
  "version": "1.0.0",
  "description": "Backend API for Smart Resume Builder",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "mongoose": "^7.5.0",
    "cors": "^2.8.5",
    "dotenv": "^16.3.1",
    "openai": "^4.0.0",
    "helmet": "^7.0.0",
    "express-rate-limit": "^6.10.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

## 2. Server.js
```javascript
const express = require('express');
const mongoose = require('mongoose');
const cors = require('cors');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
require('dotenv').config();

const app = express();

// Middleware
app.use(helmet());
app.use(cors());
app.use(express.json());

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});
app.use(limiter);

// MongoDB connection
mongoose.connect(process.env.MONGODB_URI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// Routes
app.use('/api/resume', require('./routes/resume'));
app.use('/api/suggestions', require('./routes/suggestions'));

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

## 3. Resume Model (models/Resume.js)
```javascript
const mongoose = require('mongoose');

const ResumeSchema = new mongoose.Schema({
  userId: {
    type: String,
    required: true,
    index: true
  },
  personalInfo: {
    fullName: String,
    email: String,
    phone: String,
    location: String,
    linkedin: String,
    website: String
  },
  education: [{
    institution: String,
    degree: String,
    field: String,
    startDate: String,
    endDate: String,
    gpa: String
  }],
  experience: [{
    company: String,
    position: String,
    startDate: String,
    endDate: String,
    current: Boolean,
    description: String,
    location: String
  }],
  skills: [String],
  projects: [{
    name: String,
    description: String,
    technologies: [String],
    link: String,
    startDate: String,
    endDate: String
  }],
  certifications: [{
    name: String,
    issuer: String,
    date: String,
    expiryDate: String,
    credentialId: String
  }]
}, { timestamps: true });

module.exports = mongoose.model('Resume', ResumeSchema);
```

## 4. Resume Routes (routes/resume.js)
```javascript
const express = require('express');
const router = express.Router();
const Resume = require('../models/Resume');

// POST /api/resume - Save resume
router.post('/', async (req, res) => {
  try {
    const { userId, ...resumeData } = req.body;
    
    let resume = await Resume.findOne({ userId });
    
    if (resume) {
      resume = await Resume.findOneAndUpdate(
        { userId },
        { ...resumeData },
        { new: true }
      );
    } else {
      resume = new Resume({ userId, ...resumeData });
      await resume.save();
    }
    
    res.json({ success: true, resume });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// GET /api/resume/:userId - Get resume
router.get('/:userId', async (req, res) => {
  try {
    const resume = await Resume.findOne({ userId: req.params.userId });
    if (!resume) {
      return res.status(404).json({ error: 'Resume not found' });
    }
    res.json(resume);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// PUT /api/resume/:userId - Update resume
router.put('/:userId', async (req, res) => {
  try {
    const resume = await Resume.findOneAndUpdate(
      { userId: req.params.userId },
      req.body,
      { new: true }
    );
    if (!resume) {
      return res.status(404).json({ error: 'Resume not found' });
    }
    res.json({ success: true, resume });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

module.exports = router;
```

## 5. AI Suggestions Route (routes/suggestions.js)
```javascript
const express = require('express');
const router = express.Router();
const OpenAI = require('openai');

// POST /api/suggestions - Get AI suggestions
router.post('/', async (req, res) => {
  try {
    const { resumeData, apiKey } = req.body;
    
    if (!apiKey) {
      return res.status(400).json({ error: 'API key required' });
    }

    const openai = new OpenAI({
      apiKey: apiKey,
    });

    const completion = await openai.chat.completions.create({
      model: "gpt-3.5-turbo",
      messages: [
        {
          role: "system",
          content: "You are a professional resume reviewer. Analyze the resume data and provide specific, actionable suggestions for improvement in JSON format with section, type, and suggestion fields."
        },
        {
          role: "user",
          content: `Please review this resume and provide 3-5 specific suggestions: ${JSON.stringify(resumeData)}`
        }
      ],
      max_tokens: 1000,
      temperature: 0.7,
    });

    const aiResponse = completion.choices[0].message.content;
    
    // Parse AI response into structured suggestions
    let suggestions;
    try {
      suggestions = JSON.parse(aiResponse);
    } catch (parseError) {
      // Fallback if AI doesn't return valid JSON
      suggestions = [
        {
          section: "Overall",
          type: "improvement",
          suggestion: aiResponse.substring(0, 300)
        }
      ];
    }

    res.json({ suggestions });
  } catch (error) {
    console.error('OpenAI API error:', error);
    res.status(500).json({ error: 'Failed to generate suggestions' });
  }
});

module.exports = router;
```

## 6. Environment Variables (.env)
```
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/resume_builder
PORT=5000
NODE_ENV=production
```

## 7. Deployment Instructions

### Backend Deployment (Render/Railway/Glitch):
1. Push code to GitHub repository
2. Connect your hosting service to the repository
3. Set environment variables in hosting dashboard
4. Deploy and note the API URL

### Frontend Deployment (Vercel/Netlify):
1. Update API endpoints to use deployed backend URL
2. Build and deploy frontend
3. Set up custom domain if needed

### MongoDB Atlas Setup:
1. Create MongoDB Atlas account
2. Create new cluster
3. Set up database user and network access
4. Get connection string for environment variables

This complete backend structure provides:
- RESTful API endpoints
- MongoDB integration
- OpenAI API integration
- Security middleware
- Rate limiting
- Error handling
- Environment configuration
