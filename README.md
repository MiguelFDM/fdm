<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Registro de Viajes</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
</head>
<body class="container mt-4">

    <h2 class="text-center">Registro de Viajes</h2>

    <form id="viajeForm" class="row g-3">
        <div class="col-md-4">
            <input type="text" id="nombre" class="form-control" placeholder="Nombre del pasajero" required>
        </div>
        <div class="col-md-4">
            <input type="text" id="dni" class="form-control" placeholder="DNI" required pattern="\d{7,8}">
        </div>
        <div class="col-md-4">
            <input type="text" id="destino" class="form-control" placeholder="Destino" required>
        </div>
        <div class="col-md-4">
            <input type="date" id="fecha" class="form-control" required>
        </div>
        <div class="col-md-4">
            <input type="number" id="monto" class="form-control" placeholder="Monto Pagado" required min="0">
        </div>
        <div class="col-md-4">
            <button type="submit" class="btn btn-primary w-100">Agregar</button>
        </div>
    </form>

    <h3 class="mt-4">Listado de Viajes</h3>
    <table class="table table-striped mt-3">
        <thead class="table-dark">
            <tr>
                <th>Nombre</th>
                <th>DNI</th>
                <th>Destino</th>
                <th>Fecha</th>
                <th>Monto Pagado</th>
                <th>Acciones</th>
            </tr>
        </thead>
        <tbody id="tablaViajes"></tbody>
    </table>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            cargarDatos();
            document.getElementById('viajeForm').addEventListener('submit', function(event) {
                event.preventDefault();
                let nombre = document.getElementById('nombre').value.trim();
                let dni = document.getElementById('dni').value.trim();
                let destino = document.getElementById('destino').value.trim();
                let fecha = document.getElementById('fecha').value;
                let monto = parseFloat(document.getElementById('monto').value).toFixed(2);

                if (!nombre || !dni || !destino || !fecha || isNaN(monto)) {
                    alert('Por favor, complete todos los campos correctamente.');
                    return;
                }

                let viajes = obtenerViajes();
                viajes.push({ nombre, dni, destino, fecha, monto });
                guardarDatos(viajes);
                actualizarTabla();
                document.getElementById('viajeForm').reset();
            });
        });

        function obtenerViajes() {
            return JSON.parse(localStorage.getItem('viajes')) || [];
        }

        function guardarDatos(viajes) {
            localStorage.setItem('viajes', JSON.stringify(viajes));
        }

        function actualizarTabla() {
            let tabla = document.getElementById('tablaViajes');
            tabla.innerHTML = '';
            let viajes = obtenerViajes();

            viajes.forEach((viaje, index) => {
                let fila = tabla.insertRow();
                fila.insertCell(0).textContent = viaje.nombre;
                fila.insertCell(1).textContent = viaje.dni;
                fila.insertCell(2).textContent = viaje.destino;
                fila.insertCell(3).textContent = viaje.fecha;
                fila.insertCell(4).textContent = `$${viaje.monto}`;

                let celdaAcciones = fila.insertCell(5);
                let btnEditar = document.createElement('button');
                btnEditar.textContent = 'Editar';
                btnEditar.className = 'btn btn-warning btn-sm me-2';
                btnEditar.onclick = function() { editarRegistro(index); };
                celdaAcciones.appendChild(btnEditar);

                let btnEliminar = document.createElement('button');
                btnEliminar.textContent = 'Eliminar';
                btnEliminar.className = 'btn btn-danger btn-sm';
                btnEliminar.onclick = function() { eliminarRegistro(index); };
                celdaAcciones.appendChild(btnEliminar);
            });
        }

        function eliminarRegistro(index) {
            let viajes = obtenerViajes();
            viajes.splice(index, 1);
            guardarDatos(viajes);
            actualizarTabla();
        }

        function editarRegistro(index) {
            let viajes = obtenerViajes();
            let viaje = viajes[index];

            document.getElementById('nombre').value = viaje.nombre;
            document.getElementById('dni').value = viaje.dni;
            document.getElementById('destino').value = viaje.destino;
            document.getElementById('fecha').value = viaje.fecha;
            document.getElementById('monto').value = viaje.monto;

            eliminarRegistro(index);
        }

        function cargarDatos() {
            actualizarTabla();
        }
    </script>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
