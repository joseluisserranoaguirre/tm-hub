// /api/hub-ai.js — Asesor IA central para Training Minds Hub

const METODOS_NOMBRES = {
  REVERSO:'REVERSO (Diabetes)',LATIDO:'LATIDO (Cardiovascular)',FORTALEZA:'FORTALEZA (Defensa Crónica)',
  ESCUDO:'ESCUDO (Inmunidad)',MENTE_BRILLANTE:'MENTE BRILLANTE (Niños)',CICLO:'CICLO (Hormonal Femenino)',
  RAIZ:'RAÍZ (Hormonal Masculino)',LUMEN:'LUMEN (Piel)',MELENA:'MELENA (Capilar)',
  PEAK:'PEAK (Fitness)',NOCTURNO:'NOCTURNO (Insomnio)',MOVE:'MOVE (Articular)',
  CUERPO_PLENO:'CUERPO PLENO (Masa Muscular)',CALMA:'CALMA (Estrés)',LIVIANA:'LIVIANA (Pérdida de Peso)',
};

const PVIP = {
  REVERSO:859880,LATIDO:1163680,FORTALEZA:1163680,ESCUDO:1073520,MENTE_BRILLANTE:860300,
  CICLO:1176280,RAIZ:1480080,LUMEN:795060,MELENA:898660,PEAK:937440,
  NOCTURNO:892080,MOVE:1144080,CUERPO_PLENO:982240,CALMA:1273580,LIVIANA:1027740,
};
const PDIST = {
  REVERSO:614200,LATIDO:831200,FORTALEZA:831200,ESCUDO:766800,MENTE_BRILLANTE:614500,
  CICLO:840200,RAIZ:1057200,LUMEN:567900,MELENA:641900,PEAK:669600,
  NOCTURNO:637200,MOVE:817200,CUERPO_PLENO:701600,CALMA:909700,LIVIANA:734100,
};

export default async function handler(req, res) {
  if (req.method === 'OPTIONS') {
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.setHeader('Access-Control-Allow-Methods', 'POST, OPTIONS');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
    return res.status(200).end();
  }
  if (req.method !== 'POST') return res.status(405).json({ error: 'Method not allowed' });
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'POST');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');

  const { pregunta, ventas, stock, red, socioNombre, rol } = req.body;
  if (!pregunta) return res.status(400).json({ error: 'Pregunta requerida' });

  // Resumen de ventas
  const totalVentas = (ventas || []).length;
  const gananciaTotal = (ventas || []).reduce((a, v) => {
    const gan = (PVIP[v.metodo_id] || 0) - (PDIST[v.metodo_id] || 0);
    return a + gan;
  }, 0);

  // Programas más vendidos
  const porMetodo = {};
  (ventas || []).forEach(v => {
    if (!porMetodo[v.metodo_id]) porMetodo[v.metodo_id] = 0;
    porMetodo[v.metodo_id]++;
  });
  const topMetodos = Object.entries(porMetodo)
    .sort((a, b) => b[1] - a[1])
    .slice(0, 3)
    .map(([id, cnt]) => `${METODOS_NOMBRES[id] || id}: ${cnt} venta(s)`)
    .join(', ') || 'Sin ventas aún';

  // Stock actual
  const stockDesc = Object.entries(stock || {})
    .filter(([, q]) => q > 0)
    .map(([id, q]) => `${id}: ${Math.round(q)}`)
    .join(', ') || 'Sin stock registrado';

  const stockTotal = Object.values(stock || {}).reduce((a, b) => a + b, 0);

  // Red (para mentores)
  const redDesc = rol === 'mentor' || rol === 'admin'
    ? `Red: ${(red || []).length} socio(s) directo(s)${(red || []).length ? ', activos este mes: ' + (red || []).filter(s => s.activo).length : ''}.`
    : '';

  // Ventas del mes actual
  const ahora = new Date();
  const vMes = (ventas || []).filter(v => {
    const d = new Date(v.created_at);
    return d.getMonth() === ahora.getMonth() && d.getFullYear() === ahora.getFullYear();
  }).length;

  const prompt = `Sos el asesor estratégico de ${socioNombre || 'el usuario'}, ${
    rol === 'mentor' ? 'Socio Mentor' : rol === 'admin' ? 'Admin de Training Minds' : 'Socio'
  } del ecosistema Training Minds × GanoExcel en Colombia.

CONTEXTO DEL NEGOCIO:
- Ventas totales registradas: ${totalVentas}
- Ventas este mes: ${vMes}
- Ganancia acumulada estimada: $${Math.round(gananciaTotal / 1000)}K COP
- Programas más vendidos: ${topMetodos}
- Stock actual (${Math.round(stockTotal)} productos en total): ${stockDesc}
${redDesc}

EL ECOSISTEMA TRAINING MINDS:
- TM-Socios: app de inventario y ventas — donde los socios registran sus ESPs, gestionan stock y registran ventas de los 15 programas
- LIVIANA: plataforma de acompañamiento nutricional 12 semanas (ya activa)
- 14 métodos más en desarrollo: REVERSO (diabetes), LATIDO (cardio), FORTALEZA (autoinmunes), ESCUDO (inmunidad), MENTE BRILLANTE (niños), CICLO (hormonal femenino), RAÍZ (hormonal masculino), LUMEN (piel), MELENA (capilar), PEAK (fitness), NOCTURNO (insomnio), MOVE (articular), CUERPO PLENO (músculo), CALMA (estrés)
- Cada programa tiene productos GanoExcel específicos + plataforma de acompañamiento
- Los paquetes ESP1/ESP2/ESP3 son la forma principal de abastecerse con descuento + bonos
- CordyGold es irreemplazable y solo viene en ESP3: lo necesitan LATIDO, FORTALEZA, CICLO, RAÍZ, PEAK y CUERPO PLENO
- Ganancias por programa van de $227K (LUMEN) hasta $422K (RAÍZ) por venta

${rol === 'mentor' || rol === 'admin' ? `
ROL DE MENTOR:
- Puede ver y gestionar la actividad de su red de socios
- Responsable de guiar a sus socios en ventas y conocimiento del producto
- Puede hacer consultoría especializada a clientes (ej: psicólogo con CALMA, nutricionista con LIVIANA)
- Su red incluye socios directos e indirectos hasta 5 generaciones` : ''}

INSTRUCCIONES:
- Respondé en español colombiano natural y cercano, como asesor de confianza
- Sé concreto y accionable — decí QUÉ hacer, no solo qué considerar
- Usá los números reales del contexto
- Si la pregunta es sobre estrategia de negocio, pensá en el ecosistema completo
- Si es sobre qué vender, priorizá programas con mayor ganancia y stock disponible
- Si es sobre crecimiento, considerá tanto ventas individuales como construcción de red
- Máximo 4 párrafos cortos. Emojis con moderación.

Pregunta: ${pregunta}`;

  try {
    const response = await fetch('https://api.anthropic.com/v1/messages', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'x-api-key': process.env.CLAUDE_API_KEY,
        'anthropic-version': '2023-06-01'
      },
      body: JSON.stringify({
        model: 'claude-sonnet-4-20250514',
        max_tokens: 700,
        messages: [{ role: 'user', content: prompt }]
      })
    });

    const data = await response.json();
    if (!data.content || !data.content[0]) throw new Error('Sin respuesta de IA');
    return res.status(200).json({ respuesta: data.content[0].text });
  } catch (e) {
    console.error('hub-ai error:', e);
    return res.status(500).json({ error: e.message });
  }
}
