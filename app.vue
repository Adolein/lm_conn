<template>
  <div class="temp-display">
    <h1>Aktuelle Temperatur</h1>
    <div class="temp-value" :class="{ cold: temp < 10, hot: temp > 30 }">
      {{ temp || '–' }} °C
    </div>
    <p v-if="!isConnected" class="status">Verbindung zum Arduino wird hergestellt...</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      temp: null,
      socket: null,
      isConnected: false
    }
  },
  mounted() {
    // Verbinde mit dem Arduino-WebSocket (ersetze IP!)
    this.socket = new WebSocket('wss://lm75.adolein.com:81');

    this.socket.onopen = () => {
      this.isConnected = true;
    };

    this.socket.onmessage = (event) => {
      const data = JSON.parse(event.data);
      this.temp = data.temp.toFixed(1); // 1 Dezimalstelle
    };

    this.socket.onerror = (error) => {
      console.error("WebSocket-Fehler:", error);
    };
  },
  beforeDestroy() {
    if (this.socket) this.socket.close();
  }
}
</script>

<style scoped>
.temp-display {
  text-align: center;
  font-family: Arial, sans-serif;
  margin-top: 50px;
}
.temp-value {
  font-size: 72px;
  font-weight: bold;
  margin: 20px;
  padding: 20px;
  border-radius: 10px;
  background: #f0f0f0;
  display: inline-block;
  min-width: 200px;
}
.cold { color: #3498db; background: #e1f5fe; }
.hot { color: #e74c3c; background: #ffebee; }
.status { color: #666; }
</style>