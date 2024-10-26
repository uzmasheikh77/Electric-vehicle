# Electric-vehicle
Electric vehicle population

1.Set Up Data Loading and Parsing

import { useEffect, useState } from 'react';
import Papa from 'papaparse';

const useData = (csvFilePath) => {
    const [data, setData] = useState([]);

    useEffect(() => {
        Papa.parse(csvFilePath, {
            download: true,
            header: true,
            complete: (results) => {
                setData(results.data);
            },
        });
    }, [csvFilePath]);

    return data;
};

export default useData;

2. Create Dashboard Components
   
import React from 'react';
import useData from './hooks/useData';
import EVTrendsChart from './components/EVTrendsChart';
import PopularModelsChart from './components/PopularModelsChart';
import BatteryRangeChart from './components/BatteryRangeChart';

const App = () => {
    const data = useData('/ev_data.csv'); // Path to CSV in the public folder

    return (
        <div className="App">
            <h1>Electric Vehicle Population Dashboard</h1>
            <EVTrendsChart data={data} />
            <PopularModelsChart data={data} />
            <BatteryRangeChart data={data} />
        </div>
    );
};

export default App;

3. Create Individual Chart Components

import React from 'react';
import { Line } from 'react-chartjs-2';

const EVTrendsChart = ({ data }) => {
    const registrationsByYear = data.reduce((acc, curr) => {
        const year = curr.year;
        acc[year] = (acc[year] || 0) + 1;
        return acc;
    }, {});

    const chartData = {
        labels: Object.keys(registrationsByYear),
        datasets: [
            {
                label: 'EV Registrations by Year',
                data: Object.values(registrationsByYear),
                fill: false,
                backgroundColor: 'blue',
                borderColor: 'lightblue',
            },
        ],
    };

    return <Line data={chartData} />;
};

export default EVTrendsChart;

4.Popular Models (Bar Chart)

import React from 'react';
import { Bar } from 'react-chartjs-2';

const PopularModelsChart = ({ data }) => {
    const modelsCount = data.reduce((acc, curr) => {
        const model = curr.model;
        acc[model] = (acc[model] || 0) + 1;
        return acc;
    }, {});

    const sortedModels = Object.entries(modelsCount).sort((a, b) => b[1] - a[1]).slice(0, 5);

    const chartData = {
        labels: sortedModels.map(([model]) => model),
        datasets: [
            {
                label: 'Top 5 Popular Models',
                data: sortedModels.map(([, count]) => count),
                backgroundColor: 'green',
            },
        ],
    };

    return <Bar data={chartData} />;
};

export default PopularModelsChart;

Battery Range Distribution (Pie Chart)
import React from 'react';
import { Pie } from 'react-chartjs-2';

const BatteryRangeChart = ({ data }) => {
    const rangeCategories = { '0-100': 0, '101-200': 0, '201-300': 0, '300+': 0 };

    data.forEach((car) => {
        const range = parseInt(car.battery_range, 10);
        if (range <= 100) rangeCategories['0-100']++;
        else if (range <= 200) rangeCategories['101-200']++;
        else if (range <= 300) rangeCategories['201-300']++;
        else rangeCategories['300+']++;
    });

    const chartData = {
        labels: Object.keys(rangeCategories),
        datasets: [
            {
                label: 'Battery Range Distribution',
                data: Object.values(rangeCategories),
                backgroundColor: ['#FF6384', '#36A2EB', '#FFCE56', '#8AC926'],
            },
        ],
    };

    return <Pie data={chartData} />;
};

export default BatteryRangeChart;

 Style the Dashboard
 .App {
    text-align: center;
    font-family: Arial, sans-serif;
}

h1 {
    color: #333;
    margin-bottom: 20px;
}

.chart-container {
    display: flex;
    justify-content: space-around;
    flex-wrap: wrap;
}

.chart-container > div {
    width: 300px;
    margin: 20px;
}


