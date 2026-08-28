---
permalink: /notas/
title: "Consulta de notas"

author_profile: false
---


<div class="notas-container">

  <h2>Consulta de calificaciones</h2>

  <p></p>

  <div class="consulta-formulario">


<input
  type="text"
  id="documento"
  placeholder="Número de documento"
  autocomplete="off"
>

<button onclick="consultarNotas()">
  Consultar
</button>


  </div>

  <div id="resultado"></div>

</div>

<style>

.notas-container {
  max-width: 700px;
  margin: 40px auto;
  padding: 30px;
}

.consulta-formulario {
  display: flex;
  gap: 10px;
  margin: 25px 0;
}

#documento {
  flex: 1;
  padding: 12px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.consulta-formulario button {
  padding: 12px 22px;
  font-size: 16px;
  cursor: pointer;
}

.tabla-notas {
  width: 100%;
  margin-top: 25px;
  border-collapse: collapse;
}

.tabla-notas th,
.tabla-notas td {
  padding: 12px;
  border-bottom: 1px solid #ddd;
  text-align: left;
}

.mensaje-error {
  margin-top: 20px;
  padding: 12px;
}

</style>

<script>

const API_URL = "https://script.google.com/macros/s/AKfycbyH7H2ifavNaFy11uahrV3hbCTKUAS2U1ek6dQnEcR_tnm9jcyTxis1pQPSV9hfXTUy0g/exec";


async function consultarNotas() {

  const documento = document
    .getElementById("documento")
    .value
    .trim();

  const resultado = document.getElementById("resultado");


  // Verificar que se ingresó un documento

  if (!documento) {

    resultado.innerHTML = `
      <div class="mensaje-error">
        Por favor, ingresa tu número de documento.
      </div>
    `;

    return;
  }


  // Mensaje mientras consulta

  resultado.innerHTML = "<p>Consultando calificaciones...</p>";


  try {

    const respuesta = await fetch(
      API_URL + "?doc=" + encodeURIComponent(documento)
    );

    const datos = await respuesta.json();


    // Si el documento no existe

    if (!datos.success) {

      resultado.innerHTML = `
        <div class="mensaje-error">
          ${datos.message}
        </div>
      `;

      return;
    }


    // Crear automáticamente las filas de la tabla

    let filas = "";

    for (const [nombre, nota] of Object.entries(datos.notas)) {

      filas += `
        <tr>
          <th>${nombre}</th>
          <td>${nota}</td>
        </tr>
      `;

    }


    // Mostrar resultados

    resultado.innerHTML = `

      <h3>Resultados</h3>

      <table class="tabla-notas">

        ${filas}

      </table>

    `;

  } catch (error) {

    console.error(error);

    resultado.innerHTML = `
      <div class="mensaje-error">
        Ocurrió un error al consultar las calificaciones.
        Intenta nuevamente más tarde.
      </div>
    `;

  }

}


// Permitir consultar presionando ENTER

document
  .getElementById("documento")
  .addEventListener("keypress", function(event) {

    if (event.key === "Enter") {

      consultarNotas();

    }

  });

</script>
