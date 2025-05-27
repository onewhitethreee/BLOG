---
uuid: ace7fb50-38d1-11f0-82f8-5d409b848782
title: jmeter configuration
date: 2025-05-24 21:02:44
tags: [jmeter]
---

Estos son los configuraciones de jmeter para estudiar examen 

![](https://img.164314.xyz/2025/05/e328cc4a959ce1e2756a5fb38b15a774.png)

![](https://img.164314.xyz/2025/05/ed718861e7836f474775f1b1016979ab.png)

![](https://img.164314.xyz/2025/05/39a613d2e6006e3d98d85d3b26208f9f.png)

![](https://img.164314.xyz/2025/05/ee0a9e9bfb2b10b8f00016bc66e8d51f.png)

![](https://img.164314.xyz/2025/05/997f812ee045f78f94e8441fd6d67138.png)

![](https://img.164314.xyz/2025/05/3fb4847e8888e191889355f09b28dd7c.png)

![](https://img.164314.xyz/2025/05/44a10efda0c8876cb93aa5a871ee785e.png)

![](https://img.164314.xyz/2025/05/14b1c8e5e18c4e9f72102db528b93517.png)

![](https://img.164314.xyz/2025/05/b52342ae93a0c2b8dd760d09eab7292b.png)

![](https://img.164314.xyz/2025/05/7ee7d36458a837cc4dfbbf61e2ddcd75.png)

![](https://img.164314.xyz/2025/05/d9796cba88ec117ff32e9ca38103151b.png)

![](https://img.164314.xyz/2025/05/53a1d13e5e6f21bcde642fa252738ff6.png)

![](https://img.164314.xyz/2025/05/fc2a7226d4814c7060b14d1b2c229fdd.png)

![](https://img.164314.xyz/2025/05/2568e53efcf2e4b42738468b8935da52.png)

![](https://img.164314.xyz/2025/05/1de02552324ac05a0342e6327651254c.png)

![](https://img.164314.xyz/2025/05/b0ee9011f4fe8ddead877e6b8a685ec9.png)

![](https://img.164314.xyz/2025/05/7e4c0fdb6ca4dcbbe64a67364fc3bb69.png)

![](https://img.164314.xyz/2025/05/fbaf7e6c64421e6af50a56f33d427b65.png)

![](https://img.164314.xyz/2025/05/1780f64594db65e437b65f44fbb1e673.png)

![](https://img.164314.xyz/2025/05/7c59f8d28cd9f9a151265a0ca492d642.png)

![](https://img.164314.xyz/2025/05/87eb2c4cff4c3307c8f66088e7b3ce38.png)

![](https://img.164314.xyz/2025/05/e3a6d9be5a88569b9cc207b8ce576969.png)

![](https://img.164314.xyz/2025/05/0469a3dd832e5d37e81bd92c6975ccb2.png)

<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Plan JMeter - Tienda de Animales</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: #333;
            line-height: 1.6;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        .header {
            text-align: center;
            color: white;
            margin-bottom: 30px;
        }
        
        .header h1 {
            font-size: 3em;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
            margin-bottom: 10px;
        }
        
        .header p {
            font-size: 1.2em;
            opacity: 0.9;
        }
        
        .main-content {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 30px;
            margin-bottom: 30px;
        }
        
        .card {
            background: white;
            border-radius: 15px;
            box-shadow: 0 15px 35px rgba(0,0,0,0.1);
            overflow: hidden;
            transition: transform 0.3s ease;
        }
        
        .card:hover {
            transform: translateY(-5px);
        }
        
        .card-header {
            background: linear-gradient(45deg, #2196F3, #21CBF3);
            color: white;
            padding: 20px;
            text-align: center;
        }
        
        .card-header h2 {
            font-size: 1.5em;
            margin-bottom: 5px;
        }
        
        .card-content {
            padding: 25px;
        }
        
        .flow-diagram {
            background: #f8f9fa;
            border-radius: 10px;
            padding: 20px;
            margin-bottom: 20px;
        }
        
        .flow-step {
            display: flex;
            align-items: center;
            margin: 15px 0;
            padding: 15px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            transition: all 0.3s ease;
        }
        
        .flow-step:hover {
            transform: translateX(10px);
            box-shadow: 0 5px 20px rgba(0,0,0,0.15);
        }
        
        .step-icon {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            margin-right: 15px;
            color: white;
            font-weight: bold;
            font-size: 18px;
        }
        
        .step-a { background: #4CAF50; }
        .step-b { background: #FF9800; }
        .step-c { background: #2196F3; }
        .step-d { background: #9C27B0; }
        .step-e { background: #F44336; }
        .step-f { background: #00BCD4; }
        .step-g { background: #795548; }
        .step-h { background: #607D8B; }
        .step-i { background: #FF5722; }
        
        .step-details {
            flex: 1;
        }
        
        .step-title {
            font-weight: bold;
            color: #1976D2;
            margin-bottom: 5px;
        }
        
        .step-description {
            color: #666;
            font-size: 0.9em;
        }
        
        .points-badge {
            background: #4CAF50;
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.8em;
            font-weight: bold;
            margin-left: 10px;
        }
        
        .iteration-badge {
            background: #FF9800;
            color: white;
            padding: 5px 10px;
            border-radius: 20px;
            font-size: 0.8em;
            margin-left: 10px;
        }
        
        .tree-structure {
            font-family: 'Courier New', monospace;
            background: #f8f9fa;
            border-radius: 10px;
            padding: 20px;
            border-left: 4px solid #2196F3;
        }
        
        .tree-item {
            margin: 8px 0;
            padding: 8px;
            border-radius: 5px;
            transition: background 0.3s ease;
        }
        
        .tree-item:hover {
            background: rgba(33, 150, 243, 0.1);
        }
        
        .level-0 { margin-left: 0px; font-weight: bold; font-size: 1.1em; color: #1976D2; }
        .level-1 { margin-left: 20px; color: #388E3C; }
        .level-2 { margin-left: 40px; color: #F57C00; }
        .level-3 { margin-left: 60px; color: #7B1FA2; }
        
        .jmeter-element {
            font-weight: bold;
            color: #D32F2F;
        }
        
        .element-name {
            color: #1976D2;
            background: #E3F2FD;
            padding: 2px 6px;
            border-radius: 4px;
            font-size: 0.9em;
        }
        
        .config-section {
            background: linear-gradient(135deg, #E8F5E8, #C8E6C9);
            border-radius: 15px;
            padding: 25px;
            margin-top: 30px;
            border-left: 5px solid #4CAF50;
        }
        
        .config-title {
            color: #2E7D32;
            font-size: 1.8em;
            margin-bottom: 20px;
            text-align: center;
        }
        
        .specs-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-top: 20px;
        }
        
        .spec-card {
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 5px 15px rgba(0,0,0,0.1);
            text-align: center;
            transition: transform 0.3s ease;
        }
        
        .spec-card:hover {
            transform: scale(1.05);
        }
        
        .spec-icon {
            font-size: 2em;
            margin-bottom: 10px;
        }
        
        .spec-label {
            font-weight: bold;
            color: #1976D2;
            margin-bottom: 5px;
        }
        
        .spec-value {
            color: #666;
            font-size: 1.1em;
        }
        
        .conditional-logic {
            background: linear-gradient(135deg, #FFF9C4, #F9FBE7);
            border-radius: 10px;
            padding: 20px;
            margin: 20px 0;
            border-left: 4px solid #FBC02D;
        }
        
        .logic-title {
            color: #F57C00;
            font-weight: bold;
            margin-bottom: 10px;
        }
        
        .logic-item {
            display: flex;
            align-items: center;
            margin: 10px 0;
            padding: 10px;
            background: white;
            border-radius: 5px;
        }
        
        .arrow {
            color: #2196F3;
            font-size: 1.5em;
            margin: 10px 0;
            text-align: center;
        }
        
        @media (max-width: 768px) {
            .main-content {
                grid-template-columns: 1fr;
            }
            
            .specs-grid {
                grid-template-columns: 1fr;
            }
            
            .header h1 {
                font-size: 2em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <div class="header">
            <h1>🧪 Plan JMeter</h1>
            <p>Escenario: Tienda de Animales - 100 Usuarios, 5 Iteraciones</p>
        </div>
        
        <div class="main-content">
            <!-- Flujo de Pasos -->
            <div class="card">
                <div class="card-header">
                    <h2>🔄 Flujo de Ejecución</h2>
                    <p>Secuencia de pasos por iteración</p>
                </div>
                <div class="card-content">
                    <div class="flow-diagram">
                        <div class="flow-step">
                            <div class="step-icon step-a">A</div>
                            <div class="step-details">
                                <div class="step-title">HTTP Request: Acceder_Tienda_Animales</div>
                                <div class="step-description">GET /tienda-animales</div>
                            </div>
                            <span class="points-badge">2p</span>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-b">B</div>
                            <div class="step-details">
                                <div class="step-title">Response Assertion: Verificar_Mensaje_Bienvenida</div>
                                <div class="step-description">Contains: "Bienvenido"</div>
                            </div>
                            <span class="points-badge">2p</span>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-c">C</div>
                            <div class="step-details">
                                <div class="step-title">HTTP Request: Login_Usuario</div>
                                <div class="step-description">POST /login (datos desde CSV)</div>
                            </div>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-d">D</div>
                            <div class="step-details">
                                <div class="step-title">Response Assertion: Verificar_Nombre_Pantalla</div>
                                <div class="step-description">Contains: username</div>
                            </div>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="conditional-logic">
                            <div class="logic-title">🔀 Lógica Condicional:</div>
                            
                            <div class="logic-item">
                                <div class="step-icon step-e" style="margin-right: 10px;">E</div>
                                <div style="flex: 1;">
                                    <strong>HTTP Request: Añadir_Perro_Carrito</strong>
                                    <div style="font-size: 0.9em; color: #666;">POST /carrito/anadir (producto_id=perro)</div>
                                </div>
                                <span class="iteration-badge">1ª, 3ª, 5ª vez</span>
                            </div>
                            
                            <div style="text-align: center; color: #666; margin: 10px 0;">O</div>
                            
                            <div class="logic-item">
                                <div class="step-icon step-f" style="margin-right: 10px;">F</div>
                                <div style="flex: 1;">
                                    <strong>HTTP Request: Añadir_Gato_Carrito</strong>
                                    <div style="font-size: 0.9em; color: #666;">POST /carrito/anadir (producto_id=gato)</div>
                                </div>
                                <span class="iteration-badge">2ª, 4ª vez</span>
                            </div>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-g">G</div>
                            <div class="step-details">
                                <div class="step-title">HTTP Request: Acceder_Catalogo_Reptiles</div>
                                <div class="step-description">GET /catalogo/reptiles</div>
                            </div>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-h">H</div>
                            <div class="step-details">
                                <div class="step-title">Response Assertion: Verificar_Palabra_Reptiles</div>
                                <div class="step-description">Contains: "reptiles"</div>
                            </div>
                        </div>
                        
                        <div class="arrow">⬇️</div>
                        
                        <div class="flow-step">
                            <div class="step-icon step-i">I</div>
                            <div class="step-details">
                                <div class="step-title">HTTP Request: Logout_Salir_Tienda</div>
                                <div class="step-description">GET /logout</div>
                            </div>
                        </div>
                        
                        <div style="text-align: center; margin: 20px 0; padding: 15px; background: #E3F2FD; border-radius: 8px;">
                            <strong>🔄 Repetir 5 veces con delay de 3 segundos</strong>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Estructura JMeter -->
            <div class="card">
                <div class="card-header">
                    <h2>🏗️ Estructura JMeter</h2>
                    <p>Jerarquía de elementos en el plan</p>
                </div>
                <div class="card-content">
                    <div class="tree-structure">
                        <div class="tree-item level-0">
                            📋 <span class="jmeter-element">Test Plan</span>: <span class="element-name">Tienda_Animales_Test</span>
                        </div>
                        
                        <div class="tree-item level-1">
                            👥 <span class="jmeter-element">Thread Group</span>: <span class="element-name">Usuarios_Tienda</span>
                            <small style="color: #666;">(100 usuarios, 5 iteraciones)</small>
                        </div>
                        
                        <div class="tree-item level-2">
                            📊 <span class="jmeter-element">CSV Data Set Config</span>: <span class="element-name">Datos_Usuarios</span>
                            <small style="color: #666;">(usuarios.csv)</small>
                        </div>
                        
                        <div class="tree-item level-2">
                            🍪 <span class="jmeter-element">HTTP Cookie Manager</span>: <span class="element-name">Gestor_Cookies</span>
                        </div>
                        
                        <div class="tree-item level-2">
                            ⚙️ <span class="jmeter-element">HTTP Request Defaults</span>: <span class="element-name">Config_Servidor</span>
                        </div>
                        
                        <div class="tree-item level-2">
                            🌐 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Acceder_Tienda_Animales</span> (A)
                        </div>
                        
                        <div class="tree-item level-2">
                            ✅ <span class="jmeter-element">Response Assertion</span>: <span class="element-name">Verificar_Mensaje_Bienvenida</span> (B)
                        </div>
                        
                        <div class="tree-item level-2">
                            🔐 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Login_Usuario</span> (C)
                        </div>
                        
                        <div class="tree-item level-2">
                            ✅ <span class="jmeter-element">Response Assertion</span>: <span class="element-name">Verificar_Nombre_Pantalla</span> (D)
                        </div>
                        
                        <div class="tree-item level-2">
                            ⏱️ <span class="jmeter-element">Constant Timer</span>: <span class="element-name">Delay_3_Segundos</span>
                        </div>
                        
                        <div class="tree-item level-2">
                            🔀 <span class="jmeter-element">If Controller</span>: <span class="element-name">Iteraciones_Impares_Perro</span>
                        </div>
                        
                        <div class="tree-item level-3">
                            🐕 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Añadir_Perro_Carrito</span> (E)
                        </div>
                        
                        <div class="tree-item level-2">
                            🔀 <span class="jmeter-element">If Controller</span>: <span class="element-name">Iteraciones_Pares_Gato</span>
                        </div>
                        
                        <div class="tree-item level-3">
                            🐱 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Añadir_Gato_Carrito</span> (F)
                        </div>
                        
                        <div class="tree-item level-2">
                            🦎 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Acceder_Catalogo_Reptiles</span> (G)
                        </div>
                        
                        <div class="tree-item level-2">
                            ✅ <span class="jmeter-element">Response Assertion</span>: <span class="element-name">Verificar_Palabra_Reptiles</span> (H)
                        </div>
                        
                        <div class="tree-item level-2">
                            🚪 <span class="jmeter-element">HTTP Request</span>: <span class="element-name">Logout_Salir_Tienda</span> (I)
                        </div>
                        
                        <div class="tree-item level-2">
                            📈 <span class="jmeter-element">View Results Tree</span>: <span class="element-name">Resultados_Detallados</span>
                        </div>
                        
                        <div class="tree-item level-2">
                            📊 <span class="jmeter-element">Summary Report</span>: <span class="element-name">Resumen_Estadisticas</span>
                        </div>
                        
                        <div class="tree-item level-2">
                            📉 <span class="jmeter-element">Graph Results</span>: <span class="element-name">Graficas_Rendimiento</span>
                        </div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- Configuración Técnica -->
        <div class="config-section">
            <h3 class="config-title">⚙️ Configuración Técnica del Test</h3>
            
            <div class="specs-grid">
                <div class="spec-card">
                    <div class="spec-icon">👥</div>
                    <div class="spec-label">Usuarios Concurrentes</div>
                    <div class="spec-value">100 threads</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">🔄</div>
                    <div class="spec-label">Iteraciones por Usuario</div>
                    <div class="spec-value">5 loops cada uno</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">⏱️</div>
                    <div class="spec-label">Delay entre Iteraciones</div>
                    <div class="spec-value">3 segundos (3000ms)</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">📊</div>
                    <div class="spec-label">Total de Ejecuciones</div>
                    <div class="spec-value">500 requests</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">🐕</div>
                    <div class="spec-label">Perros al Carrito</div>
                    <div class="spec-value">Iteraciones 1, 3, 5</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">🐱</div>
                    <div class="spec-label">Gatos al Carrito</div>
                    <div class="spec-value">Iteraciones 2, 4</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">📋</div>
                    <div class="spec-label">Archivo de Datos</div>
                    <div class="spec-value">usuarios.csv</div>
                </div>
                
                <div class="spec-card">
                    <div class="spec-icon">🎯</div>
                    <div class="spec-label">Puntos de Validación</div>
                    <div class="spec-value">4 puntos (A:2p, B:2p)</div>
                </div>
            </div>
            
            <div style="background: white; padding: 20px; border-radius: 10px; margin-top: 20px;">
                <h4 style="color: #1976D2; margin-bottom: 15px;">📝 Archivo usuarios.csv requerido:</h4>
                <div style="background: #f5f5f5; padding: 15px; border-radius: 5px; font-family: monospace;">
                    username,password<br>
                    user1,pass1<br>
                    user2,pass2<br>
                    user3,pass3<br>
                    ...<br>
                    user100,pass100
                </div>
            </div>
        </div>
    </div>
</body>
</html>