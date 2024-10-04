<template>
  <div class="container">
    <!-- <h1 class="title">System Health Monitor</h1> -->
    <div class="grid">
      <div class="card" v-for="(data, index) in paginatedData" :key="index">
        <div class="card-header">
          <h2>{{ data.hostname }}</h2>
          <span class="timestamp">{{ formatTimestamp(data.timestamp) }}</span>
        </div>
        <div class="card-body">
          <div class="metric">
            <i class="fas fa-desktop"></i>
            <span class="label">CPU Model:</span>
            <span>{{ data.cpu_model }}</span>
          </div>
          <div class="metric">
            <i class="fas fa-memory"></i>
            <span class="label">Memory:</span>
            <span>{{ data.used_memory }} / {{ data.total_memory }} ({{ getMemoryPercentage(data.used_memory, data.total_memory) }}%)</span>
          </div>
          <div class="metric">
            <i class="fas fa-clock"></i>
            <span class="label">Uptime:</span>
            <span>{{ data.uptime }}</span>
          </div>
          <div class="metric">
            <i class="fas fa-wifi"></i>
            <span class="label">WiFi:</span>
            <span>{{ data.wifi }}</span>
          </div>
          <div class="metric">
            <i class="fas fa-network-wired"></i>
            <span class="label">IP Address:</span>
            <span>{{ data.ip }}</span> <!-- Displaying the IP address -->
          </div>
          <div class="metric battery-status">
            <span class="label">Battery:</span>
            <div class="battery-container">
              <div class="battery">
                <div class="battery-fill" :style="{ width: batteryPercentage(data.battery) + '%' }"></div>
              </div>
              <span class="percentage">{{ batteryPercentage(data.battery) }}%</span>
              <span class="battery-state">{{ batteryState(data.battery) }}</span>
              <span class="time-to-empty">{{ batteryTimeToEmpty(data.battery) }}</span>
            </div>
          </div>
          <div class="metric ssh-status">
            <i class="fas fa-server"></i>
            <span class="label">SSH Info:</span>
            <span :class="{'ssh-active': isSshActive(data.ssh_info), 'ssh-inactive': isSshInactive(data.ssh_info)}">
              {{ data.ssh_info }}
            </span>
          </div>
        </div>
      </div>
    </div>
    <div class="pagination">
      <button @click="currentPage--" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="currentPage++" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<script>
import '@fortawesome/fontawesome-free/css/all.css';

export default {
  data() {
    return {
      currentPage: 1,
      itemsPerPage: 9,
      systemDataList: [] // Initialize empty array for system data
    };
  },
  computed: {
    paginatedData() {
      const start = (this.currentPage - 1) * this.itemsPerPage;
      return this.systemDataList.slice(start, start + this.itemsPerPage);
    },
    totalPages() {
      return Math.ceil(this.systemDataList.length / this.itemsPerPage);
    }
  },
  methods: {
    formatTimestamp(timestamp) {
      const date = new Date(timestamp);
      return date.toLocaleString();
    },
    batteryPercentage(batteryInfo) {
      const percentageMatch = batteryInfo.match(/percentage:\s*(\d+)%/);
      return percentageMatch ? Math.min(100, parseInt(percentageMatch[1], 10)) : 0; // Extract battery percentage
    },
    batteryTimeToEmpty(batteryInfo) {
      const timeMatch = batteryInfo.match(/time to full:\s*([\d.]+ minutes)/);
      return timeMatch ? timeMatch[1] : 'N/A'; // Extract time to full or return 'N/A'
    },
    batteryState(batteryInfo) {
      const stateMatch = batteryInfo.match(/state:\s*(\w+)/);
      return stateMatch ? stateMatch[1] : 'unknown'; // Extract battery state
    },
    getMemoryPercentage(usedMemory, totalMemory) {
      const used = parseFloat(usedMemory);
      const total = parseFloat(totalMemory);
      return ((used / total) * 100).toFixed(2); // Returns memory usage as a percentage
    },
    isSshActive(sshInfo) {
      return sshInfo && sshInfo.toLowerCase().includes('active ssh connections');
    },
    isSshInactive(sshInfo) {
      return sshInfo && sshInfo.toLowerCase().includes('no active ssh connections');
    },
    async fetchData() {
      try {
        const response = await fetch('/datas.json');
        if (!response.ok) throw new Error('Failed to fetch data');
        const data = await response.json();
        
        // Flatten the data structure and extract system info
        data.forEach(item => {
          // Extract all possible system info keys
          for (const key in item) {
            const systemInfo = item[key]; // Adjusted to handle dynamic keys
            if (systemInfo) {
              this.systemDataList.push(systemInfo);
            }
          }
        });
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    }
  },
  mounted() {
    this.fetchData(); // Fetch data when component is mounted
  }
};
</script>









  
  <style scoped>
  .title {
    text-align: center;
    color: #333;
    margin-bottom: 20px;
  }
  
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
    gap: 20px;
  }
  
  .card {
    background-color: #fff;
    border-radius: 8px;
    border: 1px solid #2196F3;
    padding: 15px;
    box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    transition: box-shadow 0.3s;
    cursor: pointer;
  }
  
  .card:hover {
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
  }
  
  .card-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .card-body {
    margin-top: 10px;
  }
  
  .metric {
    display: flex;
    align-items: center;
    margin: 18px 0;
  }
  
  .metric .label {
    font-weight: bold;
    margin-right: 5px;
  }
  
  .metric i {
    color: #2196F3; /* Set the icon color to blue */
    margin-right: 15px;
  }
  
  .battery-status {
    display: flex;
    align-items: center;
  }
  
  .battery-container {
    display: flex;
    align-items: center;
  }
  
  .battery {
    width: 40px;
    height: 15px;
    border: 1px solid #2196F3;
    border-radius: 5px;
    position: relative;
    overflow: hidden;
  }
  
  .battery-fill {
    height: 100%;
    background: #2196F3; /* Fill battery with blue color */
    transition: width 0.3s ease;
  }
  
  .percentage {
    font-weight: bold;
    color: #2196F3;
    font-size: 16px;
    margin-left: 10px;
  }
  
  .time-to-empty {
    margin-left: 10px;
    font-weight: bold;
    color: #333;
  }
  
  .battery-state {
    margin-left: 10px;
  }
  
  /* SSH info styles based on active/inactive status */
  .ssh-active {
    color: green; /* Active SSH connections displayed in green */
  }
  
  .ssh-inactive {
    color: red; /* No active SSH connections displayed in red */
  }
  
  .pagination {
    display: flex;
    justify-content: center;
    margin-top: 20px;
  }
  
  .pagination button {
    padding: 10px 15px;
    margin: 0 5px;
    border: none;
    background-color: #2196F3;
    color: white;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .pagination button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }
  
  .pagination span {
    align-self: center;
    margin: 0 10px;
  }
  </style>
  
   
  
  