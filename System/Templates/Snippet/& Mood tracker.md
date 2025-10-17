
```dataviewjs
//-----------------------------------------------------
// CONFIGURAÇÃO
//-----------------------------------------------------
const startDate = moment('<% tp.system.prompt("StartDate (YYYY-MM-DD)")%>', 'YYYY-MM-DD');
const endDate = moment('<% tp.date.now("YYYY-MM-DD") %>', 'YYYY-MM-DD');

const moodScale = {
  "😄 – Happy": 5, "🙂 – Neutral": 4, "😐 – Meh": 3, "😞 – Sad": 2, "😠 – Frustrated": 1
};

const moodColors = { 5: '#4caf50', 4: '#8bc34a', 3: '#ffc107', 2: '#ff9800', 1: '#f44336' };
const moodLabels = Object.fromEntries(Object.entries(moodScale).map(([k, v]) => [v, k]));

const labels = [], data = [], colors = [], pointStyles = [];
const moodCount = {}; // contador por humor
let totalValue = 0;

for (let p of dv.pages('#calendar/daily').sort(p => p.file.name, 'asc')) {
  const d = moment(p.file.name, 'YYYY-MM-DD');
  const mood = p["daily-mood"];
  const value = moodScale[mood];

  if (d.isBetween(startDate, endDate, null, '[]') && value) {
    labels.push(d.format('DD/MM/YYYY'));
    data.push(value);
    colors.push(moodColors[value]);
    pointStyles.push('circle');

    // Contagem de cada humor
    moodCount[value] = (moodCount[value] || 0) + 1;

    // Soma para média
    totalValue += value;
  }
}

// --- GRÁFICO ---
const chartData = {
  type: 'line',
  data: {
    labels,
    datasets: [{
      data,
      borderColor: '#7e57c2',
      backgroundColor: 'rgba(126, 87, 194, 0.1)',
      pointBackgroundColor: colors,
      pointBorderColor: colors,
      borderWidth: 2,
      tension: 0.3,
      fill: true,
      pointRadius: 5,
      pointHoverRadius: 7,
      pointStyle: pointStyles
    }]
  },
  options: {
    responsive: true,
    maintainAspectRatio: false,
    interaction: { intersect: false, mode: 'index' },
    scales: {
      y: {
        min: 0.5, max: 5.5,
        ticks: {
          stepSize: 1,
          callback: val => moodLabels[val] || val,
          font: { size: 11 }
        },
        title: { display: true, text: 'Estado Emocional', font: { size: 12, weight: 'bold' } },
        grid: { color: 'rgba(200, 200, 200, 0.1)' }
      },
      x: {
        ticks: { autoSkip: true, maxRotation: 45, minRotation: 30, font: { size: 10 } },
        title: { display: true, text: 'Data', font: { size: 12, weight: 'bold' } },
        grid: { display: false }
      }
    },
    plugins: {
      tooltip: {
        callbacks: {
          label: ctx => `Humor: ${moodLabels[ctx.raw] || ctx.raw}`,
          title: ctx => moment(ctx[0].label, 'DD/MM/YYYY').format('DD MMMM YYYY')
        },
        displayColors: true,
        backgroundColor: 'rgba(30, 30, 30, 0.9)',
        titleFont: { size: 12 }, bodyFont: { size: 12 }
      },
      legend: { display: false },
      title: { display: false }
    }
  }
};

Object.assign(this.container.style, { height: '450px', width: '100%' });
window.renderChart(chartData, this.container);


```
