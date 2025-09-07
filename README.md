<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Control Carretillas Hidráulicas</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        :root {
            --color-azul: #007bff;
            --color-celeste: #17a2b8;
            --color-amarillo: #ffc107;
        }
        
        body {
            background-color: #f8f9fa;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        .dashboard-header {
            background: linear-gradient(135deg, #2c3e50, #4ca1af);
            color: white;
            padding: 20px 0;
            margin-bottom: 20px;
        }
        
        .card {
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            transition: transform 0.3s;
            margin-bottom: 20px;
        }
        
        .card:hover {
            transform: translateY(-5px);
        }
        
        .card-header {
            border-top-left-radius: 10px !important;
            border-top-right-radius: 10px !important;
            font-weight: bold;
        }
        
        .table-container {
            max-height: 500px;
            overflow-y: auto;
        }
        
        .estado-operativa {
            background-color: #d4edda;
        }
        
        .estado-inoperativa {
            background-color: #f8d7da;
        }
        
        .estado-mantenimiento {
            background-color: #fff3cd;
        }
        
        .estado-sustraccion {
            background-color: #e2e3e5;
        }
        
        .badge-operativa {
            background-color: #28a745;
        }
        
        .badge-inoperativa {
            background-color: #dc3545;
        }
        
        .badge-mantenimiento {
            background-color: #ffc107;
            color: #212529;
        }
        
        .badge-sustraccion {
            background-color: #6c757d;
        }
        
        .search-box {
            border-radius: 20px;
        }
        
        .btn-floating {
            position: fixed;
            bottom: 30px;
            right: 30px;
            border-radius: 50%;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
        }
        
        .color-azul {
            color: var(--color-azul);
        }
        
        .color-celeste {
            color: var(--color-celeste);
        }
        
        .color-amarillo {
            color: var(--color-amarillo);
        }
        
        .modal-content {
            border-radius: 15px;
        }
        
        .filter-badge {
            cursor: pointer;
        }
    </style>
</head>
<body>
    <!-- Header -->
    <header class="dashboard-header">
        <div class="container">
            <div class="row align-items-center">
                <div class="col-md-6">
                    <h1><i class="fas fa-pallet"></i> Control de Carretillas Hidráulicas</h1>
                    <p class="lead">Sistema de gestión y seguimiento de carretillas</p>
                </div>
                <div class="col-md-6 text-end">
                    <button class="btn btn-light me-2" id="exportBtn"><i class="fas fa-file-export"></i> Exportar</button>
                    <button class="btn btn-light" id="printBtn"><i class="fas fa-print"></i> Imprimir</button>
                </div>
            </div>
        </div>
    </header>

    <div class="container">
        <!-- Filtros y búsqueda -->
        <div class="card mb-4">
            <div class="card-body">
                <div class="row">
                    <div class="col-md-8">
                        <div class="input-group mb-3">
                            <span class="input-group-text"><i class="fas fa-search"></i></span>
                            <input type="text" id="searchInput" class="form-control search-box" placeholder="Buscar por código, número, color, estado...">
                            <button class="btn btn-primary" id="searchBtn">Buscar</button>
                        </div>
                    </div>
                    <div class="col-md-4">
                        <select id="filterEstado" class="form-select">
                            <option value="">Todos los estados</option>
                            <option value="OPERATIVA">Operativa</option>
                            <option value="INOPERATIVA">Inoperativa</option>
                            <option value="MANTENIMIENTO">Mantenimiento</option>
                            <option value="SUSTRACCIÓN">Sustracción</option>
                        </select>
                    </div>
                </div>
                <div class="row mt-2">
                    <div class="col">
                        <span class="badge filter-badge bg-primary" data-filter="all">Todos</span>
                        <span class="badge filter-badge bg-success" data-filter="OPERATIVA">Operativas</span>
                        <span class="badge filter-badge bg-danger" data-filter="INOPERATIVA">Inoperativas</span>
                        <span class="badge filter-badge bg-warning text-dark" data-filter="MANTENIMIENTO">Mantenimiento</span>
                        <span class="badge filter-badge bg-secondary" data-filter="SUSTRACCIÓN">Sustracción</span>
                        <span class="badge filter-badge color-azul" data-filter="Azul">Color Azul</span>
                        <span class="badge filter-badge color-celeste" data-filter="Celeste">Color Celeste</span>
                        <span class="badge filter-badge color-amarillo" data-filter="Amarillo">Color Amarillo</span>
                    </div>
                </div>
            </div>
        </div>

        <!-- Estadísticas -->
        <div class="row">
            <div class="col-md-3">
                <div class="card text-white bg-primary">
                    <div class="card-body">
                        <h5 class="card-title">Total Carretillas</h5>
                        <h2 id="totalCount" class="card-text">47</h2>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card text-white bg-success">
                    <div class="card-body">
                        <h5 class="card-title">Operativas</h5>
                        <h2 id="operativasCount" class="card-text">34</h2>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card text-white bg-danger">
                    <div class="card-body">
                        <h5 class="card-title">Inoperativas</h5>
                        <h2 id="inoperativasCount" class="card-text">11</h2>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="card text-white bg-warning">
                    <div class="card-body">
                        <h5 class="card-title">Mantenimiento</h5>
                        <h2 id="mantenimientoCount" class="card-text">1</h2>
                    </div>
                </div>
            </div>
        </div>

        <!-- Gráficos -->
        <div class="row mt-4">
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">Estado de Carretillas</div>
                    <div class="card-body">
                        <canvas id="estadoChart" height="250"></canvas>
                    </div>
                </div>
            </div>
            <div class="col-md-6">
                <div class="card">
                    <div class="card-header">Distribución por Color</div>
                    <div class="card-body">
                        <canvas id="colorChart" height="250"></canvas>
                    </div>
                </div>
            </div>
        </div>

        <!-- Tabla de datos -->
        <div class="card mt-4">
            <div class="card-header d-flex justify-content-between align-items-center">
                <span>Listado de Carretillas</span>
                <button class="btn btn-sm btn-success" id="addBtn"><i class="fas fa-plus"></i> Agregar</button>
            </div>
            <div class="card-body p-0">
                <div class="table-container">
                    <table class="table table-striped table-hover mb-0">
                        <thead class="table-dark sticky-top">
                            <tr>
                                <th>N°</th>
                                <th>ITEM</th>
                                <th>ESTADO</th>
                                <th>CONTROL</th>
                                <th>UBICACIÓN</th>
                                <th>CÓDIGO</th>
                                <th>TAMAÑO</th>
                                <th>COLOR</th>
                                <th>ESTADO OPER.</th>
                                <th>ACCIONES</th>
                            </tr>
                        </thead>
                        <tbody id="dataTable">
                            <!-- Los datos se cargarán dinámicamente -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>
    </div>

    <!-- Botón flotante para ir arriba -->
    <button class="btn btn-primary btn-floating" id="goTopBtn">
        <i class="fas fa-arrow-up"></i>
    </button>

    <!-- Modal para editar/agregar -->
    <div class="modal fade" id="editModal" tabindex="-1" aria-hidden="true">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalTitle">Editar Carretilla</h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
                </div>
                <div class="modal-body">
                    <form id="carretillaForm">
                        <input type="hidden" id="editId">
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editNumero" class="form-label">N°</label>
                                    <input type="number" class="form-control" id="editNumero" required>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editItem" class="form-label">ITEM</label>
                                    <input type="number" class="form-control" id="editItem">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editEstado" class="form-label">Estado</label>
                                    <select class="form-select" id="editEstado">
                                        <option value="ACTIVA">ACTIVA</option>
                                        <option value="INACTIVA">INACTIVA</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editControl" class="form-label">Control</label>
                                    <input type="text" class="form-control" id="editControl">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editUbicacion" class="form-label">Ubicación</label>
                                    <input type="text" class="form-control" id="editUbicacion">
                                </div>
                            </div>
                            <div class="col-md-6">
                                <div class="mb-3">
                                    <label for="editCodigo" class="form-label">Código</label>
                                    <input type="text" class="form-control" id="editCodigo">
                                </div>
                            </div>
                        </div>
                        <div class="row">
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="editTamanio" class="form-label">Tamaño</label>
                                    <select class="form-select" id="editTamanio">
                                        <option value="Mediano">Mediano</option>
                                        <option value="Grande">Grande</option>
                                        <option value="Pequeño">Pequeño</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="editColor" class="form-label">Color</label>
                                    <select class="form-select" id="editColor">
                                        <option value="Azul">Azul</option>
                                        <option value="Celeste">Celeste</option>
                                        <option value="Amarillo">Amarillo</option>
                                    </select>
                                </div>
                            </div>
                            <div class="col-md-4">
                                <div class="mb-3">
                                    <label for="editEstadoOper" class="form-label">Estado Operativo</label>
                                    <select class="form-select" id="editEstadoOper">
                                        <option value="OPERATIVA">OPERATIVA</option>
                                        <option value="INOPERATIVA">INOPERATIVA</option>
                                        <option value="MANTENIMIENTO">MANTENIMIENTO</option>
                                        <option value="SUSTRACCIÓN">SUSTRACCIÓN</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                        <div class="mb-3">
                            <label for="editObservaciones" class="form-label">Observaciones</label>
                            <textarea class="form-control" id="editObservaciones" rows="3"></textarea>
                        </div>
                    </form>
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
                    <button type="button" class="btn btn-danger" id="deleteBtn">Eliminar</button>
                    <button type="button" class="btn btn-primary" id="saveBtn">Guardar</button>
                </div>
            </div>
        </div>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
    <script>
        // Datos iniciales extraídos de las imágenes
        const initialData = [
            {numero: 1, item: 16, estado: "ACTIVA", control: "PERCO3733", ubicacion: "SALIDAS", codigo: "22023", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 2, item: 4, estado: "ACTIVA", control: "PERCO3738", ubicacion: "SALIDAS", codigo: "13304", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 3, item: 4, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "14198", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 4, item: 4, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "15235", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 5, item: 6, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "19259", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 6, item: 7, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "20607", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 7, item: 8, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "222020", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 8, item: 9, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "220219", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 9, item: 10, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "220214", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 10, item: 11, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "280043", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 11, item: 12, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "240017", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 12, item: 13, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "240010", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 13, item: 14, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "7026", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 14, item: 15, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SALIDAS", codigo: "240018", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 15, item: 16, estado: "ACTIVA", control: "PERCO3739", ubicacion: "FEBRERO5", codigo: "22396", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 16, item: 17, estado: "ACTIVA", control: "PERCO3739", ubicacion: "FEBRERO5", codigo: "13306", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 17, item: 18, estado: "ACTIVA", control: "PERCO3739", ubicacion: "FEBRERO5", codigo: "16440", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 18, item: 19, estado: "ACTIVA", control: "PERCO3739", ubicacion: "RECEPCION", codigo: "210390", tamanio: "Mediano", color: "Azul", estadoOper: "MANTENIMIENTO"},
            {numero: 19, item: 20, estado: "ACTIVA", control: "PERCO3739", ubicacion: "RECEPCION", codigo: "210393", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 20, item: 21, estado: "ACTIVA", control: "PERCO3739", ubicacion: "CONTROL ALMACEN", codigo: "13301", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 21, item: 22, estado: "ACTIVA", control: "PERCO3739", ubicacion: "CONTROL ALMACEN", codigo: "213091", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 22, item: 23, estado: "ACTIVA", control: "PERCO3739", ubicacion: "ABANDONO", codigo: "240013", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 23, item: 24, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "17330", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 24, item: 25, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "16348", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 25, item: 26, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "18343", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 26, item: 27, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "17234", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 27, item: 28, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "18341", tamanio: "Mediano", color: "Celeste", estadoOper: "OPERATIVA"},
            {numero: 28, item: 29, estado: "ACTIVA", control: "PERCO3739", ubicacion: "SERV_INTERMEDIO", codigo: "18342", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 29, item: 30, estado: "ACTIVA", control: "PERC009974", ubicacion: "SERV. INTERMEDIO", codigo: "240042", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 30, item: 31, estado: "ACTIVA", control: "PERC009973", ubicacion: "SERV. INTERMEDIO", codigo: "240041", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 31, item: 32, estado: "ACTIVA", control: "PERC006417", ubicacion: "SERV. INTERMEDIO", codigo: "43286", tamanio: "Mediano", color: "Azul", estadoOper: "OPERATIVA"},
            {numero: 32, item: 33, estado: "ACTIVA", control: "PERE006464", ubicacion: "SAUDAS", codigo: "14293", tamanio: "MEDIANO", color: "CELESTE", estadoOper: "INOPERATIVA"},
            {numero: 33, item: 34, estado: "ACTIVA", control: "PERE004773", ubicacion: "SAUDAS", codigo: "6859", tamanio: "MEDIANO", color: "CELESTE", estadoOper: "INOPERATIVA"},
            {numero: 34, item: 35, estado: "ACTIVA", control: "PERE004809", ubicacion: "SERV. INTERMEDIO", codigo: "14199", tamanio: "MEDIANO", color: "AZUL", estadoOper: "INOPERATIVA"},
            {numero: 35, item: 36, estado: "ACTIVA", control: "PERE004771", ubicacion: "SERV. INTERMEDIO", codigo: "669052", tamanio: "MEDIANO", color: "CELESTE", estadoOper: "INOPERATIVA"},
            {numero: 36, item: 37, estado: "ACTIVA", control: "PERE006526", ubicacion: "SERV. INTERMEDIO", codigo: "675", tamanio: "GRANDE", color: "CELESTE", estadoOper: "INOPERATIVA"},
            {numero: 37, item: 38, estado: "ACTIVA", control: "PERE004767", ubicacion: "SAUDAS", codigo: "24287", tamanio: "MEDIANO", color: "CELESTE", estadoOper: "INOPERATIVA"},
            {numero: 38, item: 39, estado: "ACTIVA", control: "PERE004794", ubicacion: "SAUDAS", codigo: "15285", tamanio: "MEDIANO", color: "AZUL", estadoOper: "INOPERATIVA"},
            {numero: 39, item: 40, estado: "ACTIVA", control: "PERE006461", ubicacion: "SAUDAS", codigo: "220215", tamanio: "MEDIANO", color: "AMARILLO", estadoOper: "INOPERATIVA"},
            {numero: 40, item: 41, estado: "ACTIVA", control: "PERE004769", ubicacion: "SAUDAS", codigo: "5930", tamanio: "MEDIANO", color: "AZUL", estadoOper: "INOPERATIVA"},
            {numero: 41, item: 42, estado: "ACTIVA", control: "PERE006523", ubicacion: "SERV. INTERMEDIO", codigo: "14199", tamanio: "MEDIANO", color: "AZUL", estadoOper: "INOPERATIVA"},
            {numero: 42, item: 43, estado: "ACTIVA", control: "", ubicacion: "SAUDAS", codigo: "14193", tamanio: "MEDIANO", color: "CELESTE", estadoOper: "SUSTRACCIÓN"}
        ];

        // Variables globales
        let carretillasData = [];
        let estadoChartInstance, colorChartInstance;

        // Inicialización
        document.addEventListener('DOMContentLoaded', function() {
            // Cargar datos desde localStorage o usar los iniciales
            const savedData = localStorage.getItem('carretillasData');
            if (savedData) {
                carretillasData = JSON.parse(savedData);
            } else {
                carretillasData = initialData;
                guardarDatos();
            }
            
            // Renderizar tabla y gráficos
            renderTable();
            updateCounts();
            renderCharts();
            
            // Configurar event listeners
            document.getElementById('searchBtn').addEventListener('click', aplicarFiltros);
            document.getElementById('searchInput').addEventListener('keyup', function(event) {
                if (event.key === 'Enter') {
                    aplicarFiltros();
                }
            });
            document.getElementById('filterEstado').addEventListener('change', aplicarFiltros);
            
            // Filtros rápidos con badges
            document.querySelectorAll('.filter-badge').forEach(badge => {
                badge.addEventListener('click', function() {
                    const filter = this.getAttribute('data-filter');
                    if (filter === 'all') {
                        document.getElementById('searchInput').value = '';
                        document.getElementById('filterEstado').value = '';
                        aplicarFiltros();
                    } else if (['OPERATIVA', 'INOPERATIVA', 'MANTENIMIENTO', 'SUSTRACCIÓN'].includes(filter)) {
                        document.getElementById('filterEstado').value = filter;
                        aplicarFiltros();
                    } else {
                        document.getElementById('searchInput').value = filter;
                        aplicarFiltros();
                    }
                });
            });
            
            // Botón para agregar nuevo registro
            document.getElementById('addBtn').addEventListener('click', function() {
                abrirModalParaNuevo();
            });
            
            // Botón para guardar en el modal
            document.getElementById('saveBtn').addEventListener('click', guardarRegistro);
            
            // Botón para eliminar en el modal
            document.getElementById('deleteBtn').addEventListener('click', eliminarRegistro);
            
            // Botón para exportar
            document.getElementById('exportBtn').addEventListener('click', exportarDatos);
            
            // Botón para imprimir
            document.getElementById('printBtn').addEventListener('click', function() {
                window.print();
            });
            
            // Botón para ir arriba
            document.getElementById('goTopBtn').addEventListener('click', function() {
                window.scrollTo({top: 0, behavior: 'smooth'});
            });
            
            // Mostrar u ocultar el botón de ir arriba según el scroll
            window.addEventListener('scroll', function() {
                const goTopBtn = document.getElementById('goTopBtn');
                if (window.scrollY > 300) {
                    goTopBtn.style.display = 'block';
                } else {
                    goTopBtn.style.display = 'none';
                }
            });
        });

        // Función para renderizar la tabla
        function renderTable(data = carretillasData) {
            const tableBody = document.getElementById('dataTable');
            tableBody.innerHTML = '';
            
            data.forEach(item => {
                const row = document.createElement('tr');
                
                // Aplicar clase según el estado operativo
                if (item.estadoOper === 'OPERATIVA') {
                    row.classList.add('estado-operativa');
                } else if (item.estadoOper === 'INOPERATIVA') {
                    row.classList.add('estado-inoperativa');
                } else if (item.estadoOper === 'MANTENIMIENTO') {
                    row.classList.add('estado-mantenimiento');
                } else if (item.estadoOper === 'SUSTRACCIÓN') {
                    row.classList.add('estado-sustraccion');
                }
                
                row.innerHTML = `
                    <td>${item.numero}</td>
                    <td>${item.item}</td>
                    <td>${item.estado}</td>
                    <td>${item.control}</td>
                    <td>${item.ubicacion}</td>
                    <td>${item.codigo}</td>
                    <td>${item.tamanio}</td>
                    <td>${item.color}</td>
                    <td><span class="badge ${getBadgeClass(item.estadoOper)}">${item.estadoOper}</span></td>
                    <td>
                        <button class="btn btn-sm btn-outline-primary edit-btn" data-id="${item.numero}">
                            <i class="fas fa-edit"></i>
                        </button>
                    </td>
                `;
                
                tableBody.appendChild(row);
            });
            
            // Agregar event listeners a los botones de editar
            document.querySelectorAll('.edit-btn').forEach(btn => {
                btn.addEventListener('click', function() {
                    const id = this.getAttribute('data-id');
                    abrirModalParaEdicion(id);
                });
            });
        }

        // Función para obtener la clase del badge según el estado
        function getBadgeClass(estadoOper) {
            switch(estadoOper) {
                case 'OPERATIVA': return 'badge-operativa';
                case 'INOPERATIVA': return 'badge-inoperativa';
                case 'MANTENIMIENTO': return 'badge-mantenimiento';
                case 'SUSTRACCIÓN': return 'badge-sustraccion';
                default: return 'badge-secondary';
            }
        }

        // Función para aplicar filtros de búsqueda
        function aplicarFiltros() {
            const searchText = document.getElementById('searchInput').value.toLowerCase();
            const filterEstado = document.getElementById('filterEstado').value;
            
            const filteredData = carretillasData.filter(item => {
                const matchesSearch = 
                    item.numero.toString().includes(searchText) ||
                    item.item.toString().includes(searchText) ||
                    item.control.toLowerCase().includes(searchText) ||
                    item.ubicacion.toLowerCase().includes(searchText) ||
                    item.codigo.toLowerCase().includes(searchText) ||
                    item.tamanio.toLowerCase().includes(searchText) ||
                    item.color.toLowerCase().includes(searchText) ||
                    item.estadoOper.toLowerCase().includes(searchText);
                
                const matchesEstado = filterEstado === '' || item.estadoOper === filterEstado;
                
                return matchesSearch && matchesEstado;
            });
            
            renderTable(filteredData);
            updateCounts(filteredData);
        }

        // Función para actualizar los contadores
        function updateCounts(data = carretillasData) {
            document.getElementById('totalCount').textContent = data.length;
            document.getElementById('operativasCount').textContent = data.filter(item => item.estadoOper === 'OPERATIVA').length;
            document.getElementById('inoperativasCount').textContent = data.filter(item => item.estadoOper === 'INOPERATIVA').length;
            document.getElementById('mantenimientoCount').textContent = data.filter(item => item.estadoOper === 'MANTENIMIENTO').length;
        }

        // Función para renderizar gráficos
        function renderCharts() {
            const ctxEstado = document.getElementById('estadoChart').getContext('2d');
            const ctxColor = document.getElementById('colorChart').getContext('2d');
            
            // Destruir instancias previas si existen
            if (estadoChartInstance) estadoChartInstance.destroy();
            if (colorChartInstance) colorChartInstance.destroy();
            
            // Datos para el gráfico de estado
            const estados = ['OPERATIVA', 'INOPERATIVA', 'MANTENIMIENTO', 'SUSTRACCIÓN'];
            const estadoCounts = estados.map(estado => 
                carretillasData.filter(item => item.estadoOper === estado).length
            );
            
            // Datos para el gráfico de colores
            const colores = ['Azul', 'Celeste', 'Amarillo'];
            const colorCounts = colores.map(color => 
                carretillasData.filter(item => item.color === color).length
            );
            
            // Crear gráfico de estado
            estadoChartInstance = new Chart(ctxEstado, {
                type: 'doughnut',
                data: {
                    labels: estados,
                    datasets: [{
                        data: estadoCounts,
                        backgroundColor: ['#28a745', '#dc3545', '#ffc107', '#6c757d']
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        },
                        title: {
                            display: true,
                            text: 'Distribución por Estado Operativo'
                        }
                    }
                }
            });
            
            // Crear gráfico de colores
            colorChartInstance = new Chart(ctxColor, {
                type: 'pie',
                data: {
                    labels: cOlores,
                    datasets: [{
                        data: colorCounts,
                        backgroundColor: ['#007bff', '#17a2b8', '#ffc107']
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'bottom'
                        },
                        title: {
                            display: true,
                            text: 'Distribución por Color'
                        }
                    }
                }
            });
        }

        // Función para abrir modal para edición
        function abrirModalParaEdicion(id) {
            const item = carretillasData.find(item => item.numero == id);
            if (!item) return;
            
            document.getElementById('modalTitle').textContent = 'Editar Carretilla';
            document.getElementById('editId').value = item.numero;
            document.getElementById('editNumero').value = item.numero;
            document.getElementById('editItem').value = item.item;
            document.getElementById('editEstado').value = item.estado;
            document.getElementById('editControl').value = item.control;
            document.getElementById('editUbicacion').value = item.ubicacion;
            document.getElementById('editCodigo').value = item.codigo;
            document.getElementById('editTamanio').value = item.tamanio;
            document.getElementById('editColor').value = item.color;
            document.getElementById('editEstadoOper').value = item.estadoOper;
            document.getElementById('editObservaciones').value = item.observaciones || '';
            
            // Mostrar botón de eliminar
            document.getElementById('deleteBtn').style.display = 'inline-block';
            
            const modal = new bootstrap.Modal(document.getElementById('editModal'));
            modal.show();
        }

        // Función para abrir modal para nuevo registro
        function abrirModalParaNuevo() {
            document.getElementById('modalTitle').textContent = 'Agregar Nueva Carretilla';
            document.getElementById('carretillaForm').reset();
            
            // Ocultar botón de eliminar
            document.getElementById('deleteBtn').style.display = 'none';
            
            // Generar un nuevo número (máximo + 1)
            const maxNumero = Math.max(...carretillasData.map(item => item.numero));
            document.getElementById('editNumero').value = maxNumero + 1;
            
            const modal = new bootstrap.Modal(document.getElementById('editModal'));
            modal.show();
        }

        // Función para guardar registro (nuevo o editado)
        function guardarRegistro() {
            const id = document.getElementById('editId').value;
            const isNew = !id;
            
            const itemData = {
                numero: parseInt(document.getElementById('editNumero').value),
                item: parseInt(document.getElementById('editItem').value) || 0,
                estado: document.getElementById('editEstado').value,
                control: document.getElementById('editControl').value,
                ubicacion: document.getElementById('editUbicacion').value,
                codigo: document.getElementById('editCodigo').value,
                tamanio: document.getElementById('editTamanio').value,
                color: document.getElementById('editColor').value,
                estadoOper: document.getElementById('editEstadoOper').value,
                observaciones: document.getElementById('editObservaciones').value
            };
            
            if (isNew) {
                // Agregar nuevo registro
                carretillasData.push(itemData);
            } else {
                // Actualizar registro existente
                const index = carretillasData.findIndex(item => item.numero == id);
                if (index !== -1) {
                    carretillasData[index] = itemData;
                }
            }
            
            // Guardar y actualizar
            guardarDatos();
            renderTable();
            updateCounts();
            renderCharts();
            
            // Cerrar modal
            const modal = bootstrap.Modal.getInstance(document.getElementById('editModal'));
            modal.hide();
            
            // Mostrar notificación
            alert(isNew ? 'Carretilla agregada con éxito!' : 'Cambios guardados con éxito!');
        }

        // Función para eliminar registro
        function eliminarRegistro() {
            if (!confirm('¿Está seguro de que desea eliminar esta carretilla?')) return;
            
            const id = document.getElementById('editId').value;
            const index = carretillasData.findIndex(item => item.numero == id);
            
            if (index !== -1) {
                carretillasData.splice(index, 1);
                guardarDatos();
                renderTable();
                updateCounts();
                renderCharts();
                
                // Cerrar modal
                const modal = bootstrap.Modal.getInstance(document.getElementById('editModal'));
                modal.hide();
                
                // Mostrar notificación
                alert('Carretilla eliminada con éxito!');
            }
        }

        // Función para guardar datos en localStorage
        function guardarDatos() {
            localStorage.setItem('carretillasData', JSON.stringify(carretillasData));
        }

        // Función para exportar datos
        function exportarDatos() {
            // Crear contenido CSV
            let csvContent = "N°,ITEM,ESTADO,CONTROL,UBICACIÓN,CÓDIGO,TAMAÑO,COLOR,ESTADO OPER.,OBSERVACIONES\n";
            
            carretillasData.forEach(item => {
                csvContent += `"${item.numero}","${item.item}","${item.estado}","${item.control}","${item.ubicacion}","${item.codigo}","${item.tamanio}","${item.color}","${item.estadoOper}","${item.observaciones || ''}"\n`;
            });
            
            // Crear blob y descargar
            const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.setAttribute("href", url);
            link.setAttribute("download", "carretillas_hidraulicas.csv");
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }
    </script>
</body>
</html>
