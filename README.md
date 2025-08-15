# wabot
Bot WhatsApp Node.js”
const { default: makeWASocket, useSingleFileAuthState, fetchLatestBaileysVersion, DisconnectReason } = require('@adiwajshing/baileys');
const { state, saveState } = useSingleFileAuthState('./auth_info.json');

async function startBot() {
    const { version } = await fetchLatestBaileysVersion();
    const sock = makeWASocket({ version, auth: state });

    // Écoute des messages
    sock.ev.on('messages.upsert', async (msg) => {
        const message = msg.messages[0];
        if (!message.message || message.key.fromMe) return;

        const text = message.message.conversation || '';
        const sender = message.key.remoteJid;

        // Réponse automatique
        if (text.toLowerCase().includes('salut')) {
            await sock.sendMessage(sender, { text: 'Salut ! Je suis ton bot 🤖' });
        }
    });

    // Gestion de la connexion
    sock.ev.on('connection.update', (update) => {
        const { connection, lastDisconnect } = update;
        if(connection === 'close') {
            if((lastDisconnect.error)?.output?.statusCode !== DisconnectReason.loggedOut) {
                startBot();
            }
        } else if(connection === 'open') {
            console.log('✅ Bot connecté !');
        }
    });

    sock.ev.on('creds.update', saveState);
}

startBot();
