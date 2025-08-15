# wabot
Bot WhatsApp Node.js”
const { default: makeWASocket, useSingleFileAuthState, fetchLatestBaileysVersion, DisconnectReason } = require('@adiwajshing/baileys');
const { state, saveState } = useSingleFileAuthState('./auth_info.json');

async function startBot() {
    const { version } = await fetchLatestBaileysVersion();
    const sock = makeWASocket({ version, auth: state });
