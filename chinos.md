Aquí tienes el Documento de Diseño de Juego (GDD) y Prompt Técnico completo y estructurado. Puedes copiar y pegar este texto para que una IA (o un programador humano) pueda recrear el juego "Los Chinos Deluxe" exactamente como lo hemos dejado, con todas las reglas, lógica y diseño visual.

Prompt / Documento de Diseño: "Los Chinos Deluxe"
Objetivo: Crear una aplicación web de juego multijugador en tiempo real llamada "Los Chinos Deluxe". Estilo: Casino elegante, oscuro, inmersivo y responsivo (Móvil y Escritorio).

1. Stack Tecnológico
Frontend: React (Functional Components, Hooks).

Estilos: Tailwind CSS (Diseño responsivo, animaciones, degradados).

Backend/DB: Firebase Firestore (Base de datos en tiempo real NoSQL).

Auth: Firebase Auth (Inicio de sesión anónimo).

Iconos: lucide-react.

Gráficos: SVGs "Inline" personalizados para manos (abiertas/cerradas) y monedas.

2. Reglas y Mecánicas del Juego
Configuración
Jugadores: De 2 a 10 jugadores (configurable por el anfitrión, defecto 4).

Rondas para ganar: Configurable (defecto 3).

Bots (IA): Si se inicia la partida sin llenar la sala, los huecos se rellenan con Bots controlados por el cliente del Anfitrión.

Flujo de la Partida (Fases)
Fase de Selección (SELECTION):

Todos los jugadores eligen en secreto 0, 1, 2 o 3 monedas.

Tiempo límite: 15 segundos.

Si se agota el tiempo, se selecciona un número aleatorio automáticamente.

Fase de Apuestas (GUESSING):

Por turnos, empezando por la "Mano" (jugador inicial de la ronda).

Cada jugador debe adivinar la suma total de monedas.

Restricción: No se puede repetir un número ya dicho en la ronda actual.

Tiempo límite: 20 segundos por turno.

Si se agota el tiempo, se realiza una apuesta aleatoria válida automáticamente.

Fase de Resolución (RESULT):

Se muestran todas las manos abiertas.

Si alguien acierta la suma exacta, gana 1 punto.

Transición:

Aparece un botón "¡Estoy Listo!" para los humanos.

Si todos los humanos están listos, pasa a la siguiente ronda.

Si no, hay un temporizador de seguridad de 10 segundos que fuerza el avance.

Fin de juego: Si alguien alcanza los puntos objetivo, se muestra la pantalla de victoria.

3. Gestión de Salas y Multijugador
Identidad: Usar un playerId generado y guardado en localStorage para persistencia (recuperar la partida si se recarga). No depender solo del UID de Firebase para permitir pruebas locales.

Creación de Sala: Generar códigos amigables basados en personajes de ficción + número (ej: BATMAN-42, GOKU-99).

Lógica de Host:

El creador es el Host.

El Host gestiona los temporizadores, los movimientos de la IA y los cambios de fase escribiendo en la base de datos.

Host Migration: Si el Host abandona la partida, el siguiente jugador humano hereda el rol de Host automáticamente.

Limpieza: Si el Host abandona en el Lobby, la sala se borra. Si la partida se queda sin humanos, la sala se borra.

4. Interfaz de Usuario (UI) y Diseño
A. Pantalla de Inicio (Login)
Fondo oscuro (slate-900).

Tarjeta central para introducir apodo.

B. Sala de Espera (Lobby)
Cabecera: Muestra el código de sala como "SALA: [NOMBRE]". Botón de salir/borrar.

Cuerpo:

Lista de jugadores con indicador de Host (corona) y Bot.
Panel de Configuración (Jugadores y Rondas) al final de la lista (solo editable por Host).

Chat integrado en la parte inferior o lateral.

Pie: Botón "INICIAR PARTIDA" (Solo Host).

C. Mesa de Juego (Inmersiva)
Layout: Debe ocupar el 100% del alto y ancho de la pantalla (absolute inset-0, overflow-hidden).

Fondo: Textura de fieltro verde (emerald-900 con patrón sutil).

Disposición: Jugadores distribuidos en círculo (trigonometría). El radio del círculo debe adaptarse al tamaño de la pantalla (más pequeño en móviles).

Elementos Flotantes (UI Overlay):

Arriba: Barra con Ronda actual, Meta de puntos y Estado (Temporizador visual).

Abajo: Controles de juego (Botones de monedas pequeños, Botonera de apuestas, Botón de confirmar).

Representación del Jugador:

Mano: SVG de puño cerrado (fase juego) o mano abierta con monedas pintadas en la palma (fase resultado).

Etiqueta: Nombre y Puntos en una "pastilla" superpuesta ligeramente sobre la muñeca de la mano (z-index superior).

Indicador de Selección: Pequeño círculo flotando a la derecha del puño que muestra al propio jugador cuántas monedas ha elegido (privado).

Bocadillo: Muestra la apuesta realizada.

Centro de la Mesa:

Fondo: Icono de monedas grande y semitransparente integrado en el tapete.

Resultado: Panel emergente con el total y el ganador (Z-Index máximo).
{
  "collection": "rooms",
  "documentId": "BATMAN-88",
  "fields": {
    "hostId": "uid_firebase",
    "hostPlayerId": "local_storage_id", // Para lógica de juego
    "status": "waiting | playing | game_over",
    "currentRound": 1,
    "phase": "selection | guessing | result",
    "phaseEndTime": 1715000000, // Timestamp para timeouts
    "turnIndex": 0,
    "startPlayerIndex": 0,
    "guessesTaken": [2, 5],
    "winner": "playerId_ganador",
    "readyPlayers": ["playerId1", "playerId2"], // Para consenso de siguiente ronda
    "config": { "maxPlayers": 4, "winningRounds": 3 },
    "chat": [{ "sender": "Ana", "text": "Hola", "timestamp": ... }],
    "players": [
      {
        "uid": "...",
        "playerId": "...",
        "name": "Jugador 1",
        "score": 0,
        "coins": -1, // -1: no seleccionado, 0-3: seleccionado
        "guess": -1, // -1: no apostado
        "isAI": false
      }
    ]
  }
}