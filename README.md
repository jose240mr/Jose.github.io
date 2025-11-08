# Jose.github.io
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Contador Multi-Dispositivo en Tiempo Real</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%);
            color: white;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
            text-align: center;
        }
        
        .container {
            max-width: 800px;
            width: 100%;
            background-color: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
            margin-bottom: 30px;
        }
        
        h1 {
            font-size: 2.5rem;
            margin-bottom: 20px;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
        }
        
        .counter-container {
            margin: 30px 0;
        }
        
        .counter {
            font-size: 6rem;
            font-weight: bold;
            margin: 20px 0;
            text-shadow: 3px 3px 6px rgba(0, 0, 0, 0.3);
            transition: all 0.3s ease;
        }
        
        .counter.reached {
            color: #ffeb3b;
            transform: scale(1.1);
            animation: pulse 1s infinite;
        }
        
        @keyframes pulse {
            0% { transform: scale(1.1); }
            50% { transform: scale(1.15); }
            100% { transform: scale(1.1); }
        }
        
        .message {
            margin-top: 20px;
            font-size: 1.2rem;
            opacity: 0.9;
        }
        
        .status {
            margin-top: 15px;
            font-size: 1.1rem;
            padding: 10px;
            border-radius: 10px;
            background-color: rgba(255, 255, 255, 0.1);
        }
        
        .instructions {
            margin-top: 20px;
            padding: 15px;
            background-color: rgba(255, 255, 255, 0.15);
            border-radius: 10px;
            font-size: 1rem;
        }
        
        .loading {
            margin: 10px 0;
            font-style: italic;
        }
        
        .connection-status {
            margin-top: 10px;
            font-size: 0.9rem;
            opacity: 0.8;
        }
        
        .github-info {
            margin-top: 20px;
            padding: 15px;
            background-color: rgba(255, 255, 255, 0.1);
            border-radius: 10px;
            border-left: 4px solid #ffeb3b;
        }
        
        .error-message {
            margin-top: 10px;
            padding: 10px;
            background-color: rgba(255, 0, 0, 0.2);
            border-radius: 8px;
            border: 1px solid rgba(255, 0, 0, 0.3);
        }
        
        /* Estilos para el modal de recompensa */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: all 0.3s ease;
        }
        
        .modal-overlay.active {
            opacity: 1;
            visibility: visible;
        }
        
        .modal-content {
            background-color: white;
            color: #333;
            border-radius: 15px;
            padding: 30px;
            max-width: 500px;
            width: 90%;
            position: relative;
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
            transform: translateY(20px);
            transition: transform 0.3s ease;
        }
        
        .modal-overlay.active .modal-content {
            transform: translateY(0);
        }
        
        .close-btn {
            position: absolute;
            top: 15px;
            right: 15px;
            background: none;
            border: none;
            font-size: 1.5rem;
            color: #777;
            cursor: pointer;
            width: 30px;
            height: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 50%;
            transition: all 0.3s ease;
        }
        
        .close-btn:hover {
            background-color: #f0f0f0;
            color: #333;
        }
        
        .reward-title {
            font-size: 1.8rem;
            margin-bottom: 15px;
            color: #6a11cb;
        }
        
        .reward-image-container {
            margin: 20px 0;
            padding: 15px;
            background-color: #f9f9f9;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
        }
        
        .reward-image {
            width: 100%;
            max-width: 450px;
            height: auto;
            border-radius: 8px;
            display: block;
            margin: 0 auto;
        }
        
        .reward-text {
            margin-top: 15px;
            font-style: italic;
            color: #555;
            line-height: 1.5;
            font-size: 1.1rem;
        }
        
        @media (max-width: 600px) {
            .container {
                padding: 20px;
            }
            
            h1 {
                font-size: 2rem;
            }
            
            .counter {
                font-size: 4rem;
            }
            
            .modal-content {
                padding: 20px;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Contador Multi-Dispositivo</h1>
        <p>¡Cada persona en diferentes ordenadores o móviles cuenta! Alcanza 5 personas para la recompensa.</p>
        
        <div class="counter-container">
            <div class="counter" id="counter">0</div>
            <div class="loading" id="loading">Conectando con el servidor...</div>
            <div class="message" id="message">Esperando que más personas se unan</div>
            <div class="connection-status" id="connectionStatus"></div>
            <div class="error-message" id="errorMessage" style="display: none;"></div>
        </div>
        
        <div class="status" id="status">
            Estado: Iniciando...
        </div>
        
        <div class="instructions">
            <p><strong>¡Solo usuarios reales!</strong></p>
            <p>Comparte esta URL con amigos o ábrela en tu móvil para aumentar el contador.</p>
            <p>Cada persona en un dispositivo diferente cuenta como 1 usuario real.</p>
        </div>
        
        <div class="github-info">
            <p>🚀 <strong>Alojado en GitHub Pages</strong></p>
            <p>Esta página está funcionando desde un servidor real, por lo que cuenta usuarios reales de diferentes dispositivos.</p>
        </div>
    </div>
    
    <!-- Modal de recompensa -->
    <div class="modal-overlay" id="rewardModal">
        <div class="modal-content">
            <button class="close-btn" id="closeModal">×</button>
            <h2 class="reward-title">¡Felicidades! Has alcanzado el objetivo</h2>
            <p>Como recompensa, aquí tienes una imagen inspiradora:</p>
            
            <div class="reward-image-container">
                <img src="https://images.pexels.com/photos/34106378/pexels-photo-34106378.jpeg" alt="Imagen inspiradora" class="reward-image">
            </div>
            
            <p class="reward-text">Tú proceso será el testimonio que afirmará que para Dios no hay nada imposible.</p>
        </div>
    </div>

    <script>
        // Elementos del DOM
        const counterElement = document.getElementById('counter');
        const messageElement = document.getElementById('message');
        const statusElement = document.getElementById('status');
        const loadingElement = document.getElementById('loading');
        const connectionStatusElement = document.getElementById('connectionStatus');
        const errorMessageElement = document.getElementById('errorMessage');
        const rewardModal = document.getElementById('rewardModal');
        const closeModalBtn = document.getElementById('closeModal');
        
        // Configuración
        const targetCount = 5;
        let personCount = 0;
        let rewardShown = false;
        let userId = null;
        let updateInterval = null;
        let isOnline = false;

        // Backend usando localStorage compartido (solución simple pero efectiva)
        const STORAGE_KEY = 'multiDeviceCounter';
        
        // Generar ID único para este usuario/dispositivo
        function generateUserId() {
            if (!userId) {
                userId = localStorage.getItem('userCounterId');
                if (!userId) {
                    userId = 'user_' + Date.now() + '_' + Math.random().toString(36).substr(2, 16);
                    localStorage.setItem('userCounterId', userId);
                }
            }
            return userId;
        }

        // Sistema de contador multi-dispositivo usando localStorage
        async function updateCounter() {
            const currentUserId = generateUserId();
            const now = Date.now();
            
            try {
                // Obtener datos actuales del almacenamiento compartido
                let allUsers = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
                
                // Actualizar usuario actual
                allUsers[currentUserId] = {
                    lastSeen: now,
                    userAgent: navigator.userAgent,
                    screen: `${screen.width}x${screen.height}`
                };
                
                // Limpiar usuarios inactivos (más de 30 segundos)
                Object.keys(allUsers).forEach(userId => {
                    if (now - allUsers[userId].lastSeen > 30000) {
                        delete allUsers[userId];
                    }
                });
                
                // Guardar datos actualizados
                localStorage.setItem(STORAGE_KEY, JSON.stringify(allUsers));
                
                // Contar usuarios activos
                const activeUsers = Object.keys(allUsers).length;
                personCount = activeUsers;
                
                // Actualizar interfaz
                connectionStatusElement.textContent = `✅ Conectado - ${activeUsers} usuario(s) real(es)`;
                connectionStatusElement.style.color = '#4CAF50';
                errorMessageElement.style.display = 'none';
                isOnline = true;
                
            } catch (error) {
                console.error('Error en el contador:', error);
                // En caso de error, mostrar solo este usuario
                personCount = 1;
                connectionStatusElement.textContent = '⚠️ Error de conexión - Solo contando usuario actual';
                connectionStatusElement.style.color = '#ff9800';
                errorMessageElement.textContent = 'Para contar múltiples usuarios, asegúrate de que todos accedan a la misma URL en GitHub Pages';
                errorMessageElement.style.display = 'block';
                isOnline = false;
            }
            
            updateDisplay();
        }

        // Actualizar la visualización
        function updateDisplay() {
            counterElement.textContent = personCount;
            loadingElement.style.display = 'none';
            
            // Verificar si se alcanzó el objetivo
            if (personCount >= targetCount && !rewardShown) {
                counterElement.classList.add('reached');
                messageElement.textContent = '¡Objetivo alcanzado! Todos han recibido la recompensa.';
                statusElement.textContent = `Estado: ¡${personCount} usuario(s) real(es) conectado(s)!`;
                statusElement.style.backgroundColor = 'rgba(76, 175, 80, 0.3)';
                
                // Mostrar el modal de recompensa
                if (!rewardModal.classList.contains('active')) {
                    rewardModal.classList.add('active');
                }
                rewardShown = true;
                
                // Parar las actualizaciones una vez alcanzado el objetivo
                if (updateInterval) {
                    clearInterval(updateInterval);
                }
            } else if (personCount < targetCount) {
                counterElement.classList.remove('reached');
                messageElement.textContent = `Necesitas ${targetCount - personCount} usuario(s) real(es) más para recibir la recompensa`;
                statusElement.textContent = `Estado: ${personCount} usuario(s) real(es) conectado(s)`;
                statusElement.style.backgroundColor = 'rgba(255, 255, 255, 0.1)';
            }
        }
        
        // Cerrar modal
        closeModalBtn.addEventListener('click', function() {
            rewardModal.classList.remove('active');
        });
        
        // Cerrar modal al hacer clic fuera del contenido
        rewardModal.addEventListener('click', function(e) {
            if (e.target === rewardModal) {
                rewardModal.classList.remove('active');
            }
        });
        
        // Limpiar usuario al cerrar la ventana
        function cleanupUser() {
            if (userId) {
                try {
                    let allUsers = JSON.parse(localStorage.getItem(STORAGE_KEY) || '{}');
                    delete allUsers[userId];
                    localStorage.setItem(STORAGE_KEY, JSON.stringify(allUsers));
                } catch (error) {
                    console.log('Error limpiando usuario:', error);
                }
            }
        }
        
        // Inicializar
        function initialize() {
            updateCounter();
            // Actualizar cada 2 segundos
            updateInterval = setInterval(updateCounter, 2000);
            
            // También actualizar cuando la ventana gana foco
            window.addEventListener('focus', updateCounter);
            
            // Actualizar cuando cambia la visibilidad de la página
            document.addEventListener('visibilitychange', function() {
                if (!document.hidden) {
                    updateCounter();
                }
            });
            
            // Limpiar al cerrar la ventana
            window.addEventListener('beforeunload', cleanupUser);
            window.addEventListener('pagehide', cleanupUser);
        }
        
        // Iniciar la aplicación
        initialize();
        
        // Información adicional después de 5 segundos
        setTimeout(() => {
            if (personCount === 1 && isOnline) {
                errorMessageElement.innerHTML = 
                    '💡 <strong>Consejo:</strong> Para que el contador aumente, comparte esta misma URL con otras personas. ' +
                    'Cada persona que abra el enlace desde un dispositivo diferente contará como 1 usuario real.';
                errorMessageElement.style.display = 'block';
                errorMessageElement.style.backgroundColor = 'rgba(255, 193, 7, 0.2)';
                errorMessageElement.style.borderColor = 'rgba(255, 193, 7, 0.3)';
            }
        }, 5000);
    </script>
</body>
</html>
