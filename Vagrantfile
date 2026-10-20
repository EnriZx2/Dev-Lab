Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  
  # Red y puertos
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.network "forwarded_port", guest: 3306, host: 3306

  # Recursos de la máquina virtual
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
    vb.cpus = 2
    vb.name = "DevLab-Server"
  end

  # Aprovisionamiento automatizado
  config.vm.provision "shell", inline: <<-SHELL
    set -e

    echo "=== 1. Instalando Docker ==="
    apt-get update -y
    apt-get install -y docker.io docker-compose-v2
    usermod -aG docker vagrant

    echo "=== 2. Crear directorio de trabajo ==="
    mkdir -p /app
    cd /app

    echo "=== 3. Generando interfaz web ==="
    cat << 'EOF' > index.html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>DevLab Infrastructure Portal</title>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        :root {
            --bg-primary: #0f172a;
            --bg-secondary: #1e293b;
            --bg-card: rgba(30, 41, 59, 0.7);
            --accent-purple: #8b5cf6;
            --accent-pink: #ec4899;
            --accent-blue: #3b82f6;
            --text-main: #f8fafc;
            --text-muted: #94a3b8;
            --success: #10b981;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background-color: var(--bg-primary); color: var(--text-main); min-height: 100vh; overflow-x: hidden; }

        /* Navigation Bar */
        nav {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 1.2rem 4rem;
            background: rgba(15, 23, 42, 0.8);
            backdrop-filter: blur(12px);
            border-bottom: 1px solid rgba(255,255,255,0.1);
            position: sticky; top: 0; z-index: 100;
        }

        .logo { font-size: 1.5rem; font-weight: 800; background: linear-gradient(135deg, var(--accent-pink), var(--accent-purple)); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
        .nav-links { display: flex; gap: 2rem; list-style: none; }
        .nav-links a { color: var(--text-muted); text-decoration: none; font-weight: 5rem; transition: 0.3s; }
        .nav-links a:hover { color: var(--text-main); }
        .btn-launch { background: linear-gradient(135deg, var(--accent-purple), var(--accent-blue)); color: white; padding: 0.6rem 1.4rem; border-radius: 8px; border: none; font-weight: 600; cursor: pointer; transition: 0.3s; }
        .btn-launch:hover { opacity: 0.9; transform: translateY(-2px); }

        /* Hero Section */
        .hero {
            position: relative;
            padding: 6rem 4rem 4rem 4rem;
            display: flex;
            flex-direction: column;
            align-items: flex-start;
            background: radial-gradient(circle at 80% 20%, rgba(139, 92, 246, 0.15) 0%, transparent 50%),
                        radial-gradient(circle at 20% 80%, rgba(236, 72, 153, 0.15) 0%, transparent 50%);
        }

        .hero-badge { background: rgba(139, 92, 246, 0.2); border: 1px solid var(--accent-purple); color: #c4b5fd; padding: 0.4rem 1rem; border-radius: 20px; font-size: 0.85rem; font-weight: 600; margin-bottom: 1.5rem; }
        .hero h1 { font-size: 3.5rem; font-weight: 800; line-height: 1.1; margin-bottom: 1rem; max-width: 800px; }
        .hero p { font-size: 1.2rem; color: var(--text-muted); max-width: 650px; margin-bottom: 2rem; }

        .cta-group { display: flex; gap: 1rem; margin-bottom: 3rem; }
        .btn-secondary { background: var(--bg-secondary); border: 1px solid rgba(255,255,255,0.1); color: white; padding: 0.8rem 1.8rem; border-radius: 8px; font-weight: 600; cursor: pointer; }

        /* Cards Grid */
        .grid-container { display: grid; grid-template-columns: repeat(auto-fit, minmax(300px, 1fr)); gap: 1.5rem; width: 100%; max-width: 1300px; margin: 0 auto; padding: 0 4rem 4rem 4rem; }
        .card { background: var(--bg-card); border: 1px solid rgba(255,255,255,0.08); border-radius: 12px; padding: 1.8rem; backdrop-filter: blur(8px); transition: 0.3s; }
        .card:hover { transform: translateY(-5px); border-color: rgba(139, 92, 246, 0.4); }

        .card-icon { width: 48px; height: 48px; border-radius: 10px; background: rgba(255,255,255,0.05); display: flex; align-items: center; justify-content: center; font-size: 1.4rem; color: var(--accent-purple); margin-bottom: 1.2rem; }
        .card h3 { font-size: 1.3rem; margin-bottom: 0.6rem; }
        .card p { color: var(--text-muted); font-size: 0.95rem; line-height: 1.5; }

        .status-pill { display: inline-flex; align-items: center; gap: 0.5rem; font-size: 0.85rem; font-weight: 600; color: var(--success); background: rgba(16, 185, 129, 0.1); padding: 0.3rem 0.8rem; border-radius: 12px; margin-top: 1rem; }
        .dot { width: 8px; height: 8px; background-color: var(--success); border-radius: 50%; }

        /* Footer */
        footer { text-align: center; padding: 2rem; border-top: 1px solid rgba(255,255,255,0.05); color: var(--text-muted); font-size: 0.9rem; }
    </style>
</head>
<body>

    <nav>
        <div class="logo"><i class="fa-solid fa-flask-vial"></i> DevLab Portal</div>
        <ul class="nav-links">
            <li><a href="#infra">Infraestructura</a></li>
            <li><a href="#servicios">Servicios</a></li>
            <li><a href="#base-datos">MySQL Database</a></li>
            <li><a href="#logs">Registros</a></li>
        </ul>
        <button class="btn-launch">Estado: Activo</button>
    </nav>

    <section class="hero">
        <div class="hero-badge"><i class="fa-solid fa-server"></i> Entorno de Laboratorio Dockerizado</div>
        <h1>Plataforma de Desarrollo DevLab </h1>
        <p>Infraestructura local automatizada mediante Vagrant, Docker Compose V2 y Ubuntu 22.04 LTS. Despliegue de servicios optimizado y persistente.</p>
        <div class="cta-group">
            <button class="btn-launch" onclick="alert('Conexión MySQL activa en localhost:3306')">Probar MySQL</button>
            <button class="btn-secondary" onclick="window.location.reload()">Actualizar Panel</button>
        </div>
    </section>

    <div class="grid-container">
        <div class="card">
            <div class="card-icon"><i class="fa-brands fa-docker"></i></div>
            <h3>Servidor Web Apache2</h3>
            <p>Contenedorized en Alpine Linux. Gestionando peticiones HTTP en el puerto 8080 del host de forma eficiente.</p>
            <div class="status-pill"><span class="dot"></span> Online (Puerto 80)</div>
        </div>

        <div class="card">
            <div class="card-icon"><i class="fa-solid fa-database"></i></div>
            <h3>Base de Datos MySQL 8.0</h3>
            <p>Instancia motorizada en MySQL con persistencia de volúmenes.</p>
            <div class="status-pill"><span class="dot"></span> Ready (Puerto 3306)</div>
        </div>

        <div class="card">
            <div class="card-icon"><i class="fa-solid fa-microchip"></i></div>
            <h3>Entorno Vagrant / VirtualBox</h3>
            <p>Ubuntu 22.04 LTS (Jammy Jellyfish). Sistema totalmente aprovisionado.</p>
            <div class="status-pill"><span class="dot"></span> Provisioned</div>
        </div>
    </div>

    <footer>
        <p>DevLab Infrastructure &</p>
    </footer>

</body>
</html>
EOF

    echo "=== 4. Generando init.sql ==="
    cat << 'EOF' > init.sql
CREATE TABLE IF NOT EXISTS system_logs (
    id INT AUTO_INCREMENT PRIMARY KEY,
    service_name VARCHAR(50) NOT NULL,
    status VARCHAR(20) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

INSERT INTO system_logs (service_name, status) VALUES 
('Apache2 Web Server', 'UP'),
('MySQL Database', 'UP');
EOF

    echo "=== 5. Generando docker-compose.yml ==="
    cat << 'EOF' > docker-compose.yml
services:
  webserver:
    image: httpd:2.4-alpine
    container_name: apache_web
    ports:
      - "80:80"
    volumes:
      - ./index.html:/usr/local/apache2/htdocs/index.html
    restart: always

  database:
    image: mysql:8.0
    container_name: mysql_db
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: devlab_db
      MYSQL_USER: user_devlab
      MYSQL_PASSWORD: devlabpass
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    restart: always

volumes:
  db_data:
EOF

    echo "=== 6. Desplegando Contenedores ==="
    docker compose up -d
  SHELL
end