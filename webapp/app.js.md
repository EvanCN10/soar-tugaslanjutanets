const express = require('express')
const morgan = require('morgan')
const fs = require('fs')

const app = express()
app.set('trust proxy', true)

fs.mkdirSync('./logs', { recursive: true })

const accessLogStream = fs.createWriteStream(
 './logs/access.log',
 { flags: 'a' }
)

morgan.token('json', (req, res) => {
const cleanIp = req.ip.includes('::ffff:') ? req.ip.replace('::ffff:', '') : req.ip;

return JSON.stringify({
   srcip: cleanIp,
   method: req.method,
   url: req.url,
   status: res.statusCode,
   response_time: res.getHeader('X-Response-Time') || 0
 })
})

app.use(
 morgan(':json', {
   stream: accessLogStream
 })
)

app.get('/', (req, res) => {
 res.send('Web App Running')
})

app.listen(3000, () => {
 console.log('Web app running on port 3000')
})
