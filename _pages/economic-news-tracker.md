---
layout: default
title: Economic News Tracker
permalink: /economic-news-tracker/
description: "Daily tracking and analysis of economic keywords in major financial news outlets"
---

<div class="economic-news-dashboard">
    <div class="dashboard-header">
        <h1>Economic News Headlines Tracker</h1>
        <p class="subtitle">Daily tracking of economic keywords in major financial news outlets</p>
    </div>

    <div class="summary-section" id="summary">
        <h2>Loading Latest Data...</h2>
        <div id="loading">Please wait while we load the latest analysis...</div>
    </div>

    <div id="categories-container">
        <h2>Keyword Categories</h2>
        <div class="categories-grid" id="categories"></div>
    </div>

    <div class="downloads-section">
        <h2>Download Data</h2>
        <p>Access the raw data for your own analysis</p>
        <div class="download-buttons">
            <a href="{{ '/assets/data/headlines.csv' | relative_url }}" class="download-btn">Daily Aggregates (CSV)</a>
            <a href="{{ '/assets/data/headlines_text.csv' | relative_url }}" class="download-btn">All Headlines (CSV)</a>
            <a href="{{ '/assets/data/summary.json' | relative_url }}" class="download-btn">Summary Data (JSON)</a>
        </div>
    </div>
</div>

<style>
.economic-news-dashboard {
    max-width: 1200px;
    margin: 0 auto;
}

.dashboard-header {
    text-align: center;
    margin-bottom: 40px;
    padding: 40px 20px;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
    color: white;
    border-radius: 12px;
}

.dashboard-header h1 {
    margin: 0;
    font-size: 2.5em;
}

.subtitle {
    font-size: 1.2em;
    margin: 10px 0 0 0;
    opacity: 0.9;
}

.summary-section {
    background: #f8f9fa;
    padding: 30px;
    border-radius: 12px;
    margin-bottom: 30px;
}

.categories-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
    gap: 20px;
    margin: 30px 0;
}

.category {
    background: white;
    padding: 25px;
    border-radius: 12px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    transition: transform 0.2s;
    border-left: 4px solid #3498db;
}

.category:hover {
    transform: translateY(-2px);
}

.category h3 {
    margin: 0 0 15px 0;
    color: #2c3e50;
}

.number {
    font-size: 2.2em;
    font-weight: bold;
    color: #e74c3c;
    display: block;
    margin: 10px 0;
}

.stats {
    color: #7f8c8d;
    font-size: 14px;
    line-height: 1.5;
}

.downloads-section {
    background: white;
    padding: 30px;
    border-radius: 12px;
    text-align: center;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.download-buttons {
    margin-top: 20px;
}

.download-btn {
    display: inline-block;
    margin: 0 10px 10px 0;
    padding: 12px 25px;
    background: #27ae60;
    color: white;
    text-decoration: none;
    border-radius: 8px;
    font-weight: 500;
    transition: background 0.2s;
}

.download-btn:hover {
    background: #229954;
    color: white;
    text-decoration: none;
}

.last-updated {
    font-size: 14px;
    color: #7f8c8d;
    margin-top: 20px;
    text-align: center;
}

@media (max-width: 768px) {
    .dashboard-header h1 {
        font-size: 2em;
    }
    
    .categories-grid {
        grid-template-columns: 1fr;
    }
    
    .download-btn {
        display: block;
        margin: 10px 0;
    }
}
</style>

<script>
async function loadEconomicData() {
    try {
        const response = await fetch('{{ "/assets/data/summary.json" | relative_url }}');
        const data = await response.json();
        
        // Update summary section
        document.getElementById('summary').innerHTML = `
            <h2>Latest Analysis Results</h2>
            <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px; margin: 20px 0;">
                <div style="text-align: center; background: white; padding: 20px; border-radius: 8px;">
                    <div style="font-size: 2em; font-weight: bold; color: #3498db;">${data.latest_scrape.total_headlines}</div>
                    <div>Headlines Analyzed</div>
                </div>
                <div style="text-align: center; background: white; padding: 20px; border-radius: 8px;">
                    <div style="font-size: 2em; font-weight: bold; color: #3498db;">${data.latest_scrape.sources_scraped.length}</div>
                    <div>News Sources</div>
                </div>
                <div style="text-align: center; background: white; padding: 20px; border-radius: 8px;">
                    <div style="font-size: 2em; font-weight: bold; color: #3498db;">${data.total_days}</div>
                    <div>Days Tracked</div>
                </div>
                <div style="text-align: center; background: white; padding: 20px; border-radius: 8px;">
                    <div style="font-size: 2em; font-weight: bold; color: #3498db;">${data.latest_scrape.relevance_score.toFixed(2)}</div>
                    <div>Relevance Score</div>
                </div>
            </div>
            <div class="last-updated">
                Latest analysis: ${data.latest_scrape.date} | 
                Data period: ${data.date_range.start} to ${data.date_range.end} | 
                Last updated: ${new Date(data.last_updated).toLocaleString()}
            </div>
        `;
        
        // Update categories
        const categoriesDiv = document.getElementById('categories');
        categoriesDiv.innerHTML = '';
        
        const sortedCategories = Object.entries(data.categories)
            .sort(([,a], [,b]) => b.latest - a.latest);
        
        sortedCategories.forEach(([name, stats]) => {
            const categoryDiv = document.createElement('div');
            categoryDiv.className = 'category';
            categoryDiv.innerHTML = `
                <h3>${name.replace(/_/g, ' ').replace(/\b\w/g, l => l.toUpperCase())}</h3>
                <span class="number">${stats.latest}</span>
                <div class="stats">
                    <strong>Today's mentions</strong><br>
                    Daily average: ${stats.average.toFixed(1)}<br>
                    Total collected: ${stats.total.toLocaleString()}
                </div>
            `;
            categoriesDiv.appendChild(categoryDiv);
        });
        
    } catch (error) {
        document.getElementById('summary').innerHTML = `
            <h2>Unable to Load Data</h2>
            <p>Please try refreshing the page. Error: ${error.message}</p>
        `;
    }
}

// Load data when page loads
document.addEventListener('DOMContentLoaded', loadEconomicData);
</script>
