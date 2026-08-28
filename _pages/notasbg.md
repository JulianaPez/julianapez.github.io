---
title: "Notas - Biología General"
permalink: /notasbg/
layout: single
author_profile: false
sidebar:
  nav: false
classes: wide
---

<div class="notas-container">

  <h2>Consulta de calificaciones</h2>

  <p>Ingresa tu número de documento para consultar tus calificaciones de Biología General.</p>

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

  if (!documento) {

    resultado.innerHTML = `
      <div class="mensaje-error">
        Por favor, ingresa tu número de documento.
      </div>
    `;

    return;
  }

  resultado.innerHTML = "<p>Consultando calificaciones...</p>";

  try {

    const respuesta = await fetch(
      API_URL +
      "?doc=" + encodeURIComponent(documento) +
      "&sheet=Hoja%202"
    );

    const datos = await respuesta.json();

    if (!datos.success) {

      resultado.innerHTML = `
        <div class="mensaje-error">
          ${datos.message}
        </div>
      `;

      return;
    }

    let filas = "";

    for (const [nombre, nota] of Object.entries(datos.notas)) {

      filas += `
        <tr>
          <th>${nombre}</th>
          <td>${nota}</td>
        </tr>
      `;

    }

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

document
  .getElementById("documento")
  .addEventListener("keypress", function(event) {

    if (event.key === "Enter") {
      consultarNotas();
    }

  });

</script>