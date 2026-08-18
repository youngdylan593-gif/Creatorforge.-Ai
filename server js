require('dotenv').config();
const express = require('express');
const { spawn } = require('child_process');
const path = require('path');
const app = express();

app.use(express.json());
app.use(express.static(path.join(__dirname, 'public')));

app.post('/api/generate/script', async (req, res) => {
    const { topic, audience, style, length, characters } = req.body;
    try {
        const response = await fetch('https://api.openai.com/v1/chat/completions', {
            method: 'POST',
            headers: {
                'Authorization': `Bearer ${process.env.OPENAI_API_KEY}`,
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                model: 'gpt-4o',
                messages: [
                    { role: 'system', content: 'You are the core engine for CreatorForge AI. Output structured JSON scenes.' },
                    { role: 'user', content: `Topic: ${topic}, Style: ${style}, Characters: ${JSON.stringify(characters)}` }
                ]
            })
        });
        const data = await response.json();
        res.json({ success: true, data: data.choices[0].message.content });
    } catch (err) {
        res.status(500).json({ success: false, error: err.message });
    }
});

app.post('/api/generate/kling-video', async (req, res) => {
    const { prompt, imageUrl } = req.body;
    try {
        const response = await fetch('https://api.evolink.ai/v1/videos/generations', {
            method: 'POST',
            headers: {
                'Authorization': `Bearer ${process.env.KLING_API_KEY}`,
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                model: 'kling-v3-text-to-video',
                prompt: prompt,
                image_start: imageUrl || null
            })
        });
        const data = await response.json();
        res.json({ success: true, taskId: data.task_id });
    } catch (err) {
        res.status(500).json({ success: false, error: err.message });
    }
});

app.post('/api/generate/grok-video', async (req, res) => {
    const { prompt, referenceImages } = req.body;
    try {
        const response = await fetch('https://api.apiframe.ai/v2/videos/generate', {
            method: 'POST',
            headers: {
                'X-API-Key': process.env.GROK_API_KEY,
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                model: 'grok-imagine-video',
                prompt: prompt,
                grokParams: { reference_images: referenceImages || [] }
            })
        });
        const data = await response.json();
        res.json({ success: true, jobId: data.jobId });
    } catch (err) {
        res.status(500).json({ success: false, error: err.message });
    }
});

app.post('/api/render/video', (req, res) => {
    const { projectId } = req.body;
    res.json({ success: true, message: 'Rendering pipeline initiated', jobId: projectId });

    const ffmpeg = spawn('ffmpeg', ['-i', 'input.mp4', 'output.mp4']);
    ffmpeg.stderr.on('data', (data) => {
        console.log(`FFmpeg progress info: ${data}`);
    });
});

app.listen(process.env.PORT || 5000, () => {
    console.log(`CreatorForge AI Engine online on port ${process.env.PORT || 5000}`);
});
