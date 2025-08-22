# MIS-master-dashboard
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Master Dashboard - HR & MD Performance Analytics</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .gradient-bg { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); }
        .metric-card { 
            background: linear-gradient(135deg, #f8faff 0%, #e8f2ff 100%); 
            transition: all 0.3s ease;
        }
        .metric-card:hover { transform: translateY(-2px); box-shadow: 0 10px 25px rgba(0,0,0,0.1); }
        .performance-excellent { background: linear-gradient(135deg, #10b981 0%, #059669 100%); }
        .performance-good { background: linear-gradient(135deg, #3b82f6 0%, #1d4ed8 100%); }
        .performance-average { background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%); }
        .performance-poor { background: linear-gradient(135deg, #ef4444 0%, #dc2626 100%); }
        .tab-active { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; }
        .employee-card { transition: all 0.3s ease; border-left: 4px solid transparent; }
        .employee-card:hover { transform: translateX(4px); box-shadow: 0 8px 25px rgba(0,0,0,0.1); }
        .employee-excellent { border-left-color: #10b981; }
        .employee-good { border-left-color: #3b82f6; }
        .employee-average { border-left-color: #f59e0b; }
        .employee-poor { border-left-color: #ef4444; }
        .chart-container { position: relative; height: 300px; }
        .progress-ring { transition: stroke-dasharray 0.3s ease; }
        .status-indicator { width: 8px; height: 8px; border-radius: 50%; display: inline-block; margin-right: 8px; }
        .status-active { background-color: #10b981; }
        .status-break { background-color: #f59e0b; }
        .status-offline { background-color: #6b7280; }
        .department-filter { cursor: pointer; transition: all 0.2s ease; }
        .department-filter:hover { background-color: #f3f4f6; }
        .department-filter.active { background-color: #667eea; color: white; }
        .report-tab { cursor: pointer; transition: all 0.2s ease; }
        .active-report-tab { border-color: #667eea; color: #667eea; }
        .report-content { display: block; }
        .report-content.hidden { display: none; }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <!-- Header -->
    <header class="gradient-bg text-white shadow-xl sticky top-0 z-50">
        <div class="container mx-auto px-6 py-4">
            <div class="flex justify-between items-center">
                <div>
                    <h1 class="text-3xl font-bold">Master Dashboard</h1>
                    <p class="text-blue-100 mt-1">HR & MD Performance Analytics Portal</p>
                </div>
                <div class="flex items-center space-x-6">
                    <div class="text-right">
                        <div class="text-sm opacity-90">Last Updated</div>
                        <div class="font-semibold" id="lastUpdated"></div>
                    </div>
                    <div class="text-right">
                        <div class="text-sm opacity-90">Active Users</div>
                        <div class="font-semibold text-green-200" id="activeUsers">24/28</div>
                    </div>
                    <div class="text-right">
                        <div class="text-sm opacity-90">Current Date</div>
                        <div class="font-semibold" id="currentDate"></div>
                    </div>
                </div>
            </div>
        </div>
    </header>

    <div class="container mx-auto px-6 py-8">
        <!-- Time Period Selector -->
        <div class="bg-white rounded-xl shadow-lg mb-8 p-6">
            <div class="flex flex-wrap justify-between items-center">
                <div>
                    <h2 class="text-xl font-bold text-gray-900 mb-2">Performance Analytics</h2>
                    <p class="text-gray-600">Track employee performance across different time periods</p>
                </div>
                <div class="flex space-x-2 mt-4 lg:mt-0">
                    <button onclick="setTimePeriod('daily')" class="time-tab tab-active px-4 py-2 rounded-lg font-medium transition-all">Daily</button>
                    <button onclick="setTimePeriod('weekly')" class="time-tab px-4 py-2 rounded-lg font-medium text-gray-600 hover:bg-gray-100 transition-all">Weekly</button>
                    <button onclick="setTimePeriod('monthly')" class="time-tab px-4 py-2 rounded-lg font-medium text-gray-600 hover:bg-gray-100 transition-all">Monthly</button>
                    <button onclick="setTimePeriod('overall')" class="time-tab px-4 py-2 rounded-lg font-medium text-gray-600 hover:bg-gray-100 transition-all">Overall</button>
                </div>
            </div>
        </div>

        <!-- Key Performance Metrics -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
            <div class="metric-card p-6 rounded-xl shadow-md border border-blue-100">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-sm text-gray-600">Total Employees</p>
                        <p class="text-3xl font-bold text-blue-600" id="totalEmployees">28</p>
                        <p class="text-xs text-green-600 mt-1">↗ +2 this month</p>
                    </div>
                    <div class="w-12 h-12 bg-blue-100 rounded-full flex items-center justify-center">
                        <svg class="w-6 h-6 text-blue-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-2.239"></path>
                        </svg>
                    </div>
                </div>
            </div>
            <div class="metric-card p-6 rounded-xl shadow-md border border-green-100">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-sm text-gray-600">Active Interns</p>
                        <p class="text-3xl font-bold text-green-600" id="totalInterns">12</p>
                        <p class="text-xs text-green-600 mt-1">↗ +3 this month</p>
                    </div>
                    <div class="w-12 h-12 bg-green-100 rounded-full flex items-center justify-center">
                        <svg class="w-6 h-6 text-green-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path>
                        </svg>
                    </div>
                </div>
            </div>
            <div class="metric-card p-6 rounded-xl shadow-md border border-purple-100">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-sm text-gray-600">Avg Performance</p>
                        <p class="text-3xl font-bold text-purple-600" id="avgPerformance">87.5%</p>
                        <p class="text-xs text-green-600 mt-1">↗ +2.3% this week</p>
                    </div>
                    <div class="w-12 h-12 bg-purple-100 rounded-full flex items-center justify-center">
                        <svg class="w-6 h-6 text-purple-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"></path>
                        </svg>
                    </div>
                </div>
            </div>
            <div class="metric-card p-6 rounded-xl shadow-md border border-orange-100">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-sm text-gray-600">Forms Submitted</p>
                        <p class="text-3xl font-bold text-orange-600" id="formsSubmitted">156</p>
                        <p class="text-xs text-green-600 mt-1">↗ +18 today</p>
                    </div>
                    <div class="w-12 h-12 bg-orange-100 rounded-full flex items-center justify-center">
                        <svg class="w-6 h-6 text-orange-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                        </svg>
                    </div>
                </div>
            </div>
        </div>

        <!-- Department Performance Overview -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h3 class="text-xl font-bold text-gray-900 mb-6">Department Performance</h3>
                <div class="chart-container">
                    <canvas id="departmentChart"></canvas>
                </div>
            </div>
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h3 class="text-xl font-bold text-gray-900 mb-6">Performance Trends</h3>
                <div class="chart-container">
                    <canvas id="trendsChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Department Filters -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
            <h3 class="text-xl font-bold text-gray-900 mb-4">Filter by Department</h3>
            <div class="flex flex-wrap gap-3">
                <button onclick="filterDepartment('all')" class="department-filter active px-4 py-2 rounded-lg text-sm font-medium">All Departments</button>
                <button onclick="filterDepartment('business-development')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">Business Development</button>
                <button onclick="filterDepartment('technology')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">Technology</button>
                <button onclick="filterDepartment('marketing')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">Marketing</button>
                <button onclick="filterDepartment('finance')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">Finance</button>
                <button onclick="filterDepartment('hr')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">HR</button>
                <button onclick="filterDepartment('operations')" class="department-filter px-4 py-2 rounded-lg text-sm font-medium">Operations</button>
            </div>
        </div>

        <!-- Employee Performance Grid -->
        <div class="bg-white rounded-xl shadow-lg p-6 mb-8">
            <div class="flex justify-between items-center mb-6">
                <h3 class="text-xl font-bold text-gray-900">Employee Performance Tracking</h3>
                <div class="flex space-x-3">
                    <button onclick="exportData()" class="px-4 py-2 bg-blue-600 text-white rounded-lg hover:bg-blue-700 transition-colors">
                        📊 Export Data
                    </button>
                    <button onclick="generateReport()" class="px-4 py-2 bg-green-600 text-white rounded-lg hover:bg-green-700 transition-colors">
                        📋 Generate Report
                    </button>
                </div>
            </div>
            
            <!-- Performance Summary Cards -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                <div class="performance-excellent text-white p-4 rounded-lg">
                    <div class="text-2xl font-bold" id="excellentCount">8</div>
                    <div class="text-sm opacity-90">Excellent (90-100%)</div>
                </div>
                <div class="performance-good text-white p-4 rounded-lg">
                    <div class="text-2xl font-bold" id="goodCount">15</div>
                    <div class="text-sm opacity-90">Good (75-89%)</div>
                </div>
                <div class="performance-average text-white p-4 rounded-lg">
                    <div class="text-2xl font-bold" id="averageCount">12</div>
                    <div class="text-sm opacity-90">Average (60-74%)</div>
                </div>
                <div class="performance-poor text-white p-4 rounded-lg">
                    <div class="text-2xl font-bold" id="poorCount">3</div>
                    <div class="text-sm opacity-90">Needs Improvement (<60%)</div>
                </div>
            </div>

            <!-- Employee Cards Grid -->
            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6" id="employeeGrid">
                <!-- Employee cards will be populated by JavaScript -->
            </div>
        </div>

        <!-- Detailed Analytics Section -->
        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
            <!-- Work Hours Analytics -->
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h3 class="text-xl font-bold text-gray-900 mb-6">Work Hours Analytics</h3>
                <div class="space-y-4">
                    <div class="flex justify-between items-center p-4 bg-gray-50 rounded-lg">
                        <div>
                            <div class="font-semibold text-gray-900">Average Daily Hours</div>
                            <div class="text-sm text-gray-600">Across all employees</div>
                        </div>
                        <div class="text-2xl font-bold text-blue-600">8.2h</div>
                    </div>
                    <div class="flex justify-between items-center p-4 bg-gray-50 rounded-lg">
                        <div>
                            <div class="font-semibold text-gray-900">Overtime Hours</div>
                            <div class="text-sm text-gray-600">This week</div>
                        </div>
                        <div class="text-2xl font-bold text-orange-600">24h</div>
                    </div>
                    <div class="flex justify-between items-center p-4 bg-gray-50 rounded-lg">
                        <div>
                            <div class="font-semibold text-gray-900">Attendance Rate</div>
                            <div class="text-sm text-gray-600">This month</div>
                        </div>
                        <div class="text-2xl font-bold text-green-600">96.5%</div>
                    </div>
                </div>
            </div>

            <!-- Task Completion Analytics -->
            <div class="bg-white rounded-xl shadow-lg p-6">
                <h3 class="text-xl font-bold text-gray-900 mb-6">Task Completion Analytics</h3>
                <div class="chart-container">
                    <canvas id="taskCompletionChart"></canvas>
                </div>
            </div>
        </div>

        <!-- Recent Activities Feed -->
        <div class="bg-white rounded-xl shadow-lg p-6">
            <h3 class="text-xl font-bold text-gray-900 mb-6">Recent Activities & Updates</h3>
            <div class="space-y-4" id="activitiesFeed">
                <!-- Activities will be populated by JavaScript -->
            </div>
        </div>
    </div>

    <!-- Employee Detail Modal -->
    <div id="employeeModal" class="hidden fixed inset-0 bg-black bg-opacity-50 z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-xl max-w-6xl w-full max-h-screen overflow-y-auto">
            <div class="p-6 border-b border-gray-200">
                <div class="flex justify-between items-center">
                    <h3 class="text-2xl font-bold text-gray-900" id="modalEmployeeName">Employee Details</h3>
                    <button onclick="closeEmployeeModal()" class="text-gray-400 hover:text-gray-600">
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
                        </svg>
                    </button>
                </div>
            </div>
            <div class="p-6" id="modalContent">
                <!-- Modal content will be populated by JavaScript -->
            </div>
        </div>
    </div>

    <script>
        // Sample employee data with comprehensive metrics
        const employeesData = [
            {
                id: 1, name: "Riya Sharma", role: "Senior Business Developer", department: "business-development",
                type: "employee", joinDate: "2023-01-15", performance: 94, status: "active",
                dailyHours: 8.5, weeklyTasks: 12, monthlyGoals: 85, overallRating: 4.7,
                recentActivities: ["Submitted 3 committee forms", "Generated 8 new leads", "Completed client presentation"],
                skills: ["Lead Generation", "Client Relations", "Presentation"], avatar: "👩‍💼",
                totalPitches: 245, totalResponses: 89, newDealsFound: 12, avgDailyActivities: 8.5,
                monthlyForms: 15, totalForms: 89, totalUploads: 34, totalLinks: 18
            },
            {
                id: 2, name: "Ankit Verma", role: "Business Development Executive", department: "business-development",
                type: "employee", joinDate: "2023-03-20", performance: 88, status: "active",
                dailyHours: 8.2, weeklyTasks: 10, monthlyGoals: 78, overallRating: 4.4,
                recentActivities: ["Updated 5 existing deals", "Made 15 client calls", "Prepared proposal documents"],
                skills: ["Cold Calling", "Deal Management", "CRM"], avatar: "👨‍💼",
                totalPitches: 198, totalResponses: 67, newDealsFound: 9, avgDailyActivities: 7.2,
                monthlyForms: 12, totalForms: 67, totalUploads: 28, totalLinks: 14
            },
            {
                id: 3, name: "Priya Patel", role: "Marketing Specialist", department: "marketing",
                type: "employee", joinDate: "2022-11-10", performance: 91, status: "active",
                dailyHours: 8.0, weeklyTasks: 14, monthlyGoals: 92, overallRating: 4.6,
                recentActivities: ["Launched social media campaign", "Created content calendar", "Analyzed campaign metrics"],
                skills: ["Digital Marketing", "Content Creation", "Analytics"], avatar: "👩‍🎨",
                totalPitches: 156, totalResponses: 78, newDealsFound: 6, avgDailyActivities: 9.1,
                monthlyForms: 18, totalForms: 124, totalUploads: 45, totalLinks: 23
            },
            {
                id: 4, name: "Rahul Singh", role: "Software Developer", department: "technology",
                type: "employee", joinDate: "2023-02-01", performance: 96, status: "active",
                dailyHours: 9.0, weeklyTasks: 8, monthlyGoals: 95, overallRating: 4.8,
                recentActivities: ["Deployed new features", "Fixed critical bugs", "Code review completed"],
                skills: ["React", "Node.js", "Database Design"], avatar: "👨‍💻",
                totalPitches: 45, totalResponses: 23, newDealsFound: 2, avgDailyActivities: 6.8,
                monthlyForms: 8, totalForms: 45, totalUploads: 67, totalLinks: 34
            },
            {
                id: 5, name: "Sneha Gupta", role: "HR Executive", department: "hr",
                type: "employee", joinDate: "2023-04-15", performance: 82, status: "active",
                dailyHours: 8.3, weeklyTasks: 11, monthlyGoals: 80, overallRating: 4.1,
                recentActivities: ["Conducted interviews", "Updated employee records", "Organized team meeting"],
                skills: ["Recruitment", "Employee Relations", "Policy Management"], avatar: "👩‍💼",
                totalPitches: 23, totalResponses: 12, newDealsFound: 1, avgDailyActivities: 7.5,
                monthlyForms: 22, totalForms: 98, totalUploads: 31, totalLinks: 12
            },
            {
                id: 6, name: "Amit Kumar", role: "Finance Analyst", department: "finance",
                type: "employee", joinDate: "2022-09-05", performance: 89, status: "active",
                dailyHours: 8.1, weeklyTasks: 9, monthlyGoals: 88, overallRating: 4.5,
                recentActivities: ["Prepared financial reports", "Budget analysis completed", "Expense tracking updated"],
                skills: ["Financial Analysis", "Excel", "Reporting"], avatar: "👨‍💼",
                totalPitches: 12, totalResponses: 8, newDealsFound: 0, avgDailyActivities: 6.2,
                monthlyForms: 14, totalForms: 87, totalUploads: 29, totalLinks: 15
            },
            {
                id: 7, name: "Kavya Reddy", role: "Operations Manager", department: "operations",
                type: "employee", joinDate: "2022-07-20", performance: 93, status: "active",
                dailyHours: 8.7, weeklyTasks: 13, monthlyGoals: 90, overallRating: 4.7,
                recentActivities: ["Streamlined processes", "Team coordination", "Quality assurance checks"],
                skills: ["Process Management", "Team Leadership", "Quality Control"], avatar: "👩‍💼",
                totalPitches: 34, totalResponses: 19, newDealsFound: 3, avgDailyActivities: 8.9,
                monthlyForms: 16, totalForms: 112, totalUploads: 38, totalLinks: 21
            },
            {
                id: 8, name: "Arjun Mehta", role: "Business Development Intern", department: "business-development",
                type: "intern", joinDate: "2024-01-10", performance: 76, status: "active",
                dailyHours: 7.5, weeklyTasks: 8, monthlyGoals: 70, overallRating: 3.8,
                recentActivities: ["Learning lead generation", "Shadowing senior team", "Completing training modules"],
                skills: ["Research", "Communication", "Learning"], avatar: "👨‍🎓",
                totalPitches: 67, totalResponses: 18, newDealsFound: 3, avgDailyActivities: 5.2,
                monthlyForms: 8, totalForms: 12, totalUploads: 15, totalLinks: 8
            },
            {
                id: 9, name: "Isha Jain", role: "Marketing Intern", department: "marketing",
                type: "intern", joinDate: "2024-01-15", performance: 81, status: "active",
                dailyHours: 7.0, weeklyTasks: 6, monthlyGoals: 75, overallRating: 4.0,
                recentActivities: ["Social media posts", "Content research", "Campaign assistance"],
                skills: ["Social Media", "Content Writing", "Design"], avatar: "👩‍🎓",
                totalPitches: 23, totalResponses: 12, newDealsFound: 1, avgDailyActivities: 4.8,
                monthlyForms: 6, totalForms: 9, totalUploads: 12, totalLinks: 6
            },
            {
                id: 10, name: "Vikram Joshi", role: "Tech Intern", department: "technology",
                type: "intern", joinDate: "2024-02-01", performance: 85, status: "active",
                dailyHours: 8.0, weeklyTasks: 7, monthlyGoals: 80, overallRating: 4.2,
                recentActivities: ["Bug fixes", "Feature testing", "Code documentation"],
                skills: ["JavaScript", "Testing", "Documentation"], avatar: "👨‍🎓",
                totalPitches: 8, totalResponses: 4, newDealsFound: 0, avgDailyActivities: 5.5,
                monthlyForms: 4, totalForms: 6, totalUploads: 18, totalLinks: 9
            }
        ];

        let currentTimePeriod = 'daily';
        let currentDepartment = 'all';

        // Initialize dashboard
        document.addEventListener('DOMContentLoaded', function() {
            initializeDashboard();
            renderEmployeeGrid();
            initializeCharts();
            updateActivitiesFeed();
            setInterval(updateRealTimeData, 30000); // Update every 30 seconds
        });

        function initializeDashboard() {
            // Set current date and time
            document.getElementById('currentDate').textContent = new Date().toLocaleDateString();
            document.getElementById('lastUpdated').textContent = new Date().toLocaleTimeString();
            
            // Update metrics based on employee data
            updateMetrics();
        }

        function updateMetrics() {
            const employees = employeesData.filter(emp => emp.type === 'employee');
            const interns = employeesData.filter(emp => emp.type === 'intern');
            
            document.getElementById('totalEmployees').textContent = employees.length;
            document.getElementById('totalInterns').textContent = interns.length;
            
            const avgPerformance = employeesData.reduce((sum, emp) => sum + emp.performance, 0) / employeesData.length;
            document.getElementById('avgPerformance').textContent = avgPerformance.toFixed(1) + '%';
            
            // Update performance counts
            const excellent = employeesData.filter(emp => emp.performance >= 90).length;
            const good = employeesData.filter(emp => emp.performance >= 75 && emp.performance < 90).length;
            const average = employeesData.filter(emp => emp.performance >= 60 && emp.performance < 75).length;
            const poor = employeesData.filter(emp => emp.performance < 60).length;
            
            document.getElementById('excellentCount').textContent = excellent;
            document.getElementById('goodCount').textContent = good;
            document.getElementById('averageCount').textContent = average;
            document.getElementById('poorCount').textContent = poor;
        }

        function setTimePeriod(period) {
            currentTimePeriod = period;
            
            // Update tab styling
            document.querySelectorAll('.time-tab').forEach(tab => {
                tab.classList.remove('tab-active');
                tab.classList.add('text-gray-600');
            });
            event.target.classList.add('tab-active');
            event.target.classList.remove('text-gray-600');
            
            // Update data based on time period
            updateDataForTimePeriod(period);
            renderEmployeeGrid();
        }

        function updateDataForTimePeriod(period) {
            // Simulate different metrics for different time periods
            const multipliers = {
                daily: { forms: 18, performance: 1.0 },
                weekly: { forms: 89, performance: 0.98 },
                monthly: { forms: 356, performance: 0.95 },
                overall: { forms: 1247, performance: 0.92 }
            };
            
            const mult = multipliers[period];
            document.getElementById('formsSubmitted').textContent = mult.forms;
            
            // Update performance based on time period
            const basePerformance = 87.5;
            document.getElementById('avgPerformance').textContent = (basePerformance * mult.performance).toFixed(1) + '%';
        }

        function filterDepartment(dept) {
            currentDepartment = dept;
            
            // Update filter button styling
            document.querySelectorAll('.department-filter').forEach(btn => {
                btn.classList.remove('active');
            });
            event.target.classList.add('active');
            
            renderEmployeeGrid();
        }

        function renderEmployeeGrid() {
            const grid = document.getElementById('employeeGrid');
            let filteredEmployees = employeesData;
            
            if (currentDepartment !== 'all') {
                filteredEmployees = employeesData.filter(emp => emp.department === currentDepartment);
            }
            
            grid.innerHTML = filteredEmployees.map(employee => {
                const performanceClass = getPerformanceClass(employee.performance);
                const statusColor = employee.status === 'active' ? 'status-active' : 'status-offline';
                
                return `
                    <div class="employee-card ${performanceClass} bg-white p-6 rounded-xl shadow-md cursor-pointer" onclick="openEmployeeModal(${employee.id})">
                        <div class="flex items-center justify-between mb-4">
                            <div class="flex items-center space-x-3">
                                <div class="text-3xl">${employee.avatar}</div>
                                <div>
                                    <h4 class="font-semibold text-gray-900">${employee.name}</h4>
                                    <p class="text-sm text-gray-600">${employee.role}</p>
                                </div>
                            </div>
                            <div class="text-right">
                                <div class="flex items-center">
                                    <span class="status-indicator ${statusColor}"></span>
                                    <span class="text-xs text-gray-500 capitalize">${employee.status}</span>
                                </div>
                                <div class="text-sm font-medium ${getPerformanceTextColor(employee.performance)}">${employee.performance}%</div>
                            </div>
                        </div>
                        
                        <div class="space-y-2 mb-4">
                            <div class="flex justify-between text-sm">
                                <span class="text-gray-600">Department:</span>
                                <span class="font-medium capitalize">${employee.department.replace('-', ' ')}</span>
                            </div>
                            <div class="flex justify-between text-sm">
                                <span class="text-gray-600">Type:</span>
                                <span class="font-medium capitalize">${employee.type}</span>
                            </div>
                            <div class="flex justify-between text-sm">
                                <span class="text-gray-600">Join Date:</span>
                                <span class="font-medium">${new Date(employee.joinDate).toLocaleDateString()}</span>
                            </div>
                        </div>
                        
                        <div class="grid grid-cols-3 gap-2 text-center text-xs">
                            <div class="bg-gray-50 p-2 rounded">
                                <div class="font-semibold text-blue-600">${employee.dailyHours}h</div>
                                <div class="text-gray-600">Daily Hrs</div>
                            </div>
                            <div class="bg-gray-50 p-2 rounded">
                                <div class="font-semibold text-green-600">${employee.weeklyTasks}</div>
                                <div class="text-gray-600">Tasks</div>
                            </div>
                            <div class="bg-gray-50 p-2 rounded">
                                <div class="font-semibold text-purple-600">${employee.overallRating}</div>
                                <div class="text-gray-600">Rating</div>
                            </div>
                        </div>
                        
                        <div class="mt-4">
                            <div class="w-full bg-gray-200 rounded-full h-2">
                                <div class="h-2 rounded-full ${getPerformanceBarColor(employee.performance)}" style="width: ${employee.performance}%"></div>
                            </div>
                        </div>
                    </div>
                `;
            }).join('');
        }

        function getPerformanceClass(performance) {
            if (performance >= 90) return 'employee-excellent';
            if (performance >= 75) return 'employee-good';
            if (performance >= 60) return 'employee-average';
            return 'employee-poor';
        }

        function getPerformanceTextColor(performance) {
            if (performance >= 90) return 'text-green-600';
            if (performance >= 75) return 'text-blue-600';
            if (performance >= 60) return 'text-yellow-600';
            return 'text-red-600';
        }

        function getPerformanceBarColor(performance) {
            if (performance >= 90) return 'bg-green-500';
            if (performance >= 75) return 'bg-blue-500';
            if (performance >= 60) return 'bg-yellow-500';
            return 'bg-red-500';
        }

        // Helper functions for generating detailed report data
        function generateProjectsData(employee) {
            const projects = [
                { name: "ERP System Development", client: "TechCorp Solutions", value: "₹15,00,000", progress: 85, status: "In Progress", statusColor: "bg-blue-100 text-blue-800", valueCreated: "Streamlined operations, 40% efficiency gain" },
                { name: "Mobile App Development", client: "HealthPlus Systems", value: "₹8,00,000", progress: 60, status: "Development", statusColor: "bg-yellow-100 text-yellow-800", valueCreated: "User engagement increased by 60%" },
                { name: "Dashboard Analytics", client: "RetailMax", value: "₹12,00,000", progress: 95, status: "Testing", statusColor: "bg-green-100 text-green-800", valueCreated: "Real-time insights, 25% cost reduction" }
            ];
            return projects.slice(0, employee.performance >= 90 ? 3 : employee.performance >= 75 ? 2 : 1);
        }

        function generateDealStats(employee) {
            const multiplier = employee.performance / 100;
            return {
                totalDeals: Math.floor(15 * multiplier),
                wonDeals: Math.floor(8 * multiplier),
                totalValue: `₹${(45 * multiplier).toFixed(1)}L`,
                avgDealSize: `₹${(3.2 * multiplier).toFixed(1)}L`
            };
        }

        function generateActivityStats(employee) {
            return {
                avgDailyActivities: employee.avgDailyActivities,
                newDealsFound: employee.newDealsFound,
                totalActivities: Math.floor(employee.avgDailyActivities * calculateTenureDays(employee.joinDate))
            };
        }

        function generatePitchStats(employee) {
            const successRate = ((employee.totalResponses / employee.totalPitches) * 100).toFixed(1);
            return {
                totalPitches: employee.totalPitches,
                avgDailyPitches: (employee.totalPitches / calculateTenureDays(employee.joinDate)).toFixed(1),
                totalResponses: employee.totalResponses,
                successRate: successRate
            };
        }

        function generateMonthlyPitchTrends(employee) {
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'];
            const maxPitches = Math.floor(employee.totalPitches / 6);
            return months.map(month => {
                const pitches = Math.floor(Math.random() * maxPitches) + 5;
                return {
                    month,
                    pitches,
                    percentage: (pitches / maxPitches) * 100
                };
            });
        }

        function generateMISStats(employee) {
            return {
                monthlyForms: employee.monthlyForms,
                totalForms: employee.totalForms,
                avgMonthly: Math.floor(employee.totalForms / calculateTenureMonths(employee.joinDate)),
                completionRate: Math.floor(85 + (employee.performance - 75) / 2)
            };
        }

        function generateFormBreakdown(employee) {
            const total = employee.totalForms;
            return [
                {
                    type: "Management Committee",
                    icon: "🏛️",
                    submitted: Math.floor(total * 0.4),
                    approved: Math.floor(total * 0.35),
                    pending: Math.floor(total * 0.05),
                    approvalRate: 88
                },
                {
                    type: "New Deal Find",
                    icon: "🎯",
                    submitted: Math.floor(total * 0.35),
                    approved: Math.floor(total * 0.32),
                    pending: Math.floor(total * 0.03),
                    approvalRate: 91
                },
                {
                    type: "Existing Deal Update",
                    icon: "📈",
                    submitted: Math.floor(total * 0.25),
                    approved: Math.floor(total * 0.22),
                    pending: Math.floor(total * 0.03),
                    approvalRate: 89
                }
            ];
        }

        function generateMonthlyFormTrends(employee) {
            const months = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'];
            const maxForms = employee.monthlyForms;
            return months.map(month => {
                const forms = Math.floor(Math.random() * maxForms) + 3;
                const approved = Math.floor(forms * 0.85);
                return {
                    month,
                    forms,
                    approved,
                    percentage: (forms / maxForms) * 100
                };
            });
        }

        function generateUploadStats(employee) {
            return {
                totalUploads: employee.totalUploads,
                totalLinks: employee.totalLinks,
                totalSize: `${(employee.totalUploads * 2.3).toFixed(1)}MB`
            };
        }

        function generateRecentUploads(employee) {
            const uploads = [
                { name: "Client Presentation.pptx", description: "Q4 Business Review Presentation", date: "2 days ago", size: "4.2MB", icon: "📊" },
                { name: "Lead Generation Report.xlsx", description: "Monthly lead analysis and metrics", date: "5 days ago", size: "1.8MB", icon: "📈" },
                { name: "Project Proposal.pdf", description: "ERP system implementation proposal", date: "1 week ago", size: "2.1MB", icon: "📄" },
                { name: "Meeting Notes.docx", description: "Client meeting minutes and action items", date: "1 week ago", size: "0.5MB", icon: "📝" }
            ];
            return uploads.slice(0, Math.min(4, Math.floor(employee.totalUploads / 8)));
        }

        function generateSharedLinks(employee) {
            const links = [
                { title: "CRM Dashboard", url: "https://crm.company.com/dashboard", description: "Real-time sales pipeline and metrics", date: "3 days ago", category: "CRM" },
                { title: "Project Repository", url: "https://github.com/company/project", description: "Source code and documentation", date: "1 week ago", category: "Development" },
                { title: "Client Portal", url: "https://client.portal.com", description: "Shared workspace for client collaboration", date: "2 weeks ago", category: "Client" }
            ];
            return links.slice(0, Math.min(3, Math.floor(employee.totalLinks / 6)));
        }

        function generateFileTypeStats(employee) {
            return [
                { type: "Documents", icon: "📄", count: Math.floor(employee.totalUploads * 0.4), size: "8.2MB" },
                { type: "Presentations", icon: "📊", count: Math.floor(employee.totalUploads * 0.3), size: "12.1MB" },
                { type: "Spreadsheets", icon: "📈", count: Math.floor(employee.totalUploads * 0.2), size: "3.4MB" },
                { type: "Images", icon: "🖼️", count: Math.floor(employee.totalUploads * 0.1), size: "5.7MB" }
            ];
        }

        function getEmployeeCommittee(department) {
            const committees = {
                'business-development': 'Business Development Committee',
                'technology': 'Technology Committee',
                'marketing': 'Marketing Committee',
                'finance': 'Finance Committee',
                'hr': 'HR Committee',
                'operations': 'Operations Committee'
            };
            return committees[department] || 'General Committee';
        }

        function getCommitteeRole(performance) {
            if (performance >= 90) return 'Committee Lead';
            if (performance >= 80) return 'Senior Member';
            if (performance >= 70) return 'Active Member';
            return 'Member';
        }

        function generateCommitteeStats(employee) {
            const baseStats = {
                meetingsAttended: Math.floor(10 + (employee.performance / 10)),
                contributions: Math.floor(5 + (employee.performance / 20)),
                attendanceRate: Math.floor(80 + (employee.performance / 5)),
                initiativeScore: Math.floor(6 + (employee.performance / 25)),
                leadershipRating: Math.floor(3 + (employee.performance / 30))
            };
            return baseStats;
        }

        function calculateTenureDays(joinDate) {
            const join = new Date(joinDate);
            const now = new Date();
            const diffTime = Math.abs(now - join);
            return Math.ceil(diffTime / (1000 * 60 * 60 * 24));
        }

        function calculateTenureMonths(joinDate) {
            const join = new Date(joinDate);
            const now = new Date();
            return Math.max(1, Math.floor((now - join) / (1000 * 60 * 60 * 24 * 30)));
        }

        function openEmployeeModal(employeeId) {
            const employee = employeesData.find(emp => emp.id === employeeId);
            if (!employee) return;
            
            document.getElementById('modalEmployeeName').textContent = employee.name + ' - Individual Performance Report';
            
            const modalContent = document.getElementById('modalContent');
            modalContent.innerHTML = `
                <!-- Report Navigation Tabs -->
                <div class="border-b border-gray-200 mb-6">
                    <nav class="flex space-x-8">
                        <button onclick="showReportTab('overview')" class="report-tab active-report-tab py-2 px-1 border-b-2 font-medium text-sm">Overview</button>
                        <button onclick="showReportTab('projects')" class="report-tab py-2 px-1 border-b-2 border-transparent text-gray-500 hover:text-gray-700 font-medium text-sm">Projects & Deals</button>
                        <button onclick="showReportTab('activities')" class="report-tab py-2 px-1 border-b-2 border-transparent text-gray-500 hover:text-gray-700 font-medium text-sm">Activities & Pitches</button>
                        <button onclick="showReportTab('forms')" class="report-tab py-2 px-1 border-b-2 border-transparent text-gray-500 hover:text-gray-700 font-medium text-sm">MIS & Forms</button>
                        <button onclick="showReportTab('uploads')" class="report-tab py-2 px-1 border-b-2 border-transparent text-gray-500 hover:text-gray-700 font-medium text-sm">Uploads & Links</button>
                    </nav>
                </div>

                <!-- Overview Tab -->
                <div id="overview-tab" class="report-content">
                    <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-4">Employee Information</h4>
                            <div class="space-y-3">
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Employee ID:</span>
                                    <span class="font-medium">EMP-${employee.id.toString().padStart(3, '0')}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Role:</span>
                                    <span class="font-medium">${employee.role}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Department:</span>
                                    <span class="font-medium capitalize">${employee.department.replace('-', ' ')}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Type:</span>
                                    <span class="font-medium capitalize">${employee.type}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Join Date:</span>
                                    <span class="font-medium">${new Date(employee.joinDate).toLocaleDateString()}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Tenure:</span>
                                    <span class="font-medium">${calculateTenure(employee.joinDate)}</span>
                                </div>
                                <div class="flex justify-between">
                                    <span class="text-gray-600">Committee:</span>
                                    <span class="font-medium">${getEmployeeCommittee(employee.department)}</span>
                                </div>
                            </div>
                            
                            <h4 class="text-lg font-semibold text-gray-900 mb-4 mt-8">Skills & Competencies</h4>
                            <div class="flex flex-wrap gap-2">
                                ${employee.skills.map(skill => `<span class="px-3 py-1 bg-blue-100 text-blue-800 rounded-full text-sm">${skill}</span>`).join('')}
                            </div>
                        </div>
                        
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-4">Performance Overview</h4>
                            <div class="space-y-4">
                                <div class="bg-gray-50 p-4 rounded-lg">
                                    <div class="flex justify-between items-center mb-2">
                                        <span class="text-gray-600">Overall Performance</span>
                                        <span class="font-bold text-xl ${getPerformanceTextColor(employee.performance)}">${employee.performance}%</span>
                                    </div>
                                    <div class="w-full bg-gray-200 rounded-full h-3">
                                        <div class="h-3 rounded-full ${getPerformanceBarColor(employee.performance)}" style="width: ${employee.performance}%"></div>
                                    </div>
                                </div>
                                
                                <div class="grid grid-cols-2 gap-4">
                                    <div class="bg-blue-50 p-4 rounded-lg text-center">
                                        <div class="text-2xl font-bold text-blue-600">${employee.dailyHours}h</div>
                                        <div class="text-sm text-gray-600">Avg Daily Hours</div>
                                    </div>
                                    <div class="bg-green-50 p-4 rounded-lg text-center">
                                        <div class="text-2xl font-bold text-green-600">${employee.weeklyTasks}</div>
                                        <div class="text-sm text-gray-600">Weekly Tasks</div>
                                    </div>
                                    <div class="bg-purple-50 p-4 rounded-lg text-center">
                                        <div class="text-2xl font-bold text-purple-600">${employee.monthlyGoals}%</div>
                                        <div class="text-sm text-gray-600">Monthly Goals</div>
                                    </div>
                                    <div class="bg-orange-50 p-4 rounded-lg text-center">
                                        <div class="text-2xl font-bold text-orange-600">${employee.overallRating}</div>
                                        <div class="text-sm text-gray-600">Overall Rating</div>
                                    </div>
                                </div>
                            </div>
                            
                            <h4 class="text-lg font-semibold text-gray-900 mb-4 mt-8">Recent Activities</h4>
                            <div class="space-y-2">
                                ${employee.recentActivities.map(activity => `
                                    <div class="flex items-center space-x-2 p-2 bg-gray-50 rounded">
                                        <div class="w-2 h-2 bg-green-500 rounded-full"></div>
                                        <span class="text-sm text-gray-700">${activity}</span>
                                    </div>
                                `).join('')}
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Projects & Deals Tab -->
                <div id="projects-tab" class="report-content hidden">
                    <div class="space-y-8">
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Current Projects & Deals</h4>
                            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                                ${generateProjectsData(employee).map(project => `
                                    <div class="bg-white border border-gray-200 rounded-lg p-6">
                                        <div class="flex justify-between items-start mb-4">
                                            <div>
                                                <h5 class="font-semibold text-gray-900">${project.name}</h5>
                                                <p class="text-sm text-gray-600">${project.client}</p>
                                            </div>
                                            <span class="px-2 py-1 text-xs rounded-full ${project.statusColor}">${project.status}</span>
                                        </div>
                                        <div class="space-y-2 text-sm">
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Value:</span>
                                                <span class="font-medium">${project.value}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Progress:</span>
                                                <span class="font-medium">${project.progress}%</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Value Created:</span>
                                                <span class="font-medium text-green-600">${project.valueCreated}</span>
                                            </div>
                                        </div>
                                        <div class="mt-4">
                                            <div class="w-full bg-gray-200 rounded-full h-2">
                                                <div class="bg-blue-500 h-2 rounded-full" style="width: ${project.progress}%"></div>
                                            </div>
                                        </div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Deal Performance Summary</h4>
                            <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                                <div class="bg-blue-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-blue-600">${generateDealStats(employee).totalDeals}</div>
                                    <div class="text-sm text-gray-600">Total Deals</div>
                                </div>
                                <div class="bg-green-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-green-600">${generateDealStats(employee).wonDeals}</div>
                                    <div class="text-sm text-gray-600">Won Deals</div>
                                </div>
                                <div class="bg-purple-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-purple-600">${generateDealStats(employee).totalValue}</div>
                                    <div class="text-sm text-gray-600">Total Value</div>
                                </div>
                                <div class="bg-orange-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-orange-600">${generateDealStats(employee).avgDealSize}</div>
                                    <div class="text-sm text-gray-600">Avg Deal Size</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Activities & Pitches Tab -->
                <div id="activities-tab" class="report-content hidden">
                    <div class="space-y-8">
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Daily Activities Overview</h4>
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                                <div class="bg-blue-50 p-6 rounded-lg">
                                    <div class="text-3xl font-bold text-blue-600">${generateActivityStats(employee).avgDailyActivities}</div>
                                    <div class="text-sm text-gray-600 mt-2">Average Daily Activities</div>
                                    <div class="text-xs text-gray-500 mt-1">Based on last 30 days</div>
                                </div>
                                <div class="bg-green-50 p-6 rounded-lg">
                                    <div class="text-3xl font-bold text-green-600">${generateActivityStats(employee).newDealsFound}</div>
                                    <div class="text-sm text-gray-600 mt-2">New Deals Found</div>
                                    <div class="text-xs text-gray-500 mt-1">This month</div>
                                </div>
                                <div class="bg-purple-50 p-6 rounded-lg">
                                    <div class="text-3xl font-bold text-purple-600">${generateActivityStats(employee).totalActivities}</div>
                                    <div class="text-sm text-gray-600 mt-2">Total Activities</div>
                                    <div class="text-xs text-gray-500 mt-1">Since joining</div>
                                </div>
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Pitch Performance & Success Rate</h4>
                            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                                <div class="space-y-4">
                                    <div class="bg-white border border-gray-200 rounded-lg p-6">
                                        <h5 class="font-semibold text-gray-900 mb-4">Pitch Statistics</h5>
                                        <div class="space-y-3">
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Total Pitches Sent:</span>
                                                <span class="font-bold text-blue-600">${generatePitchStats(employee).totalPitches}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Average Daily Pitches:</span>
                                                <span class="font-bold text-green-600">${generatePitchStats(employee).avgDailyPitches}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Responses Received:</span>
                                                <span class="font-bold text-purple-600">${generatePitchStats(employee).totalResponses}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Success Rate:</span>
                                                <span class="font-bold text-orange-600">${generatePitchStats(employee).successRate}%</span>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                                <div>
                                    <div class="bg-white border border-gray-200 rounded-lg p-6">
                                        <h5 class="font-semibold text-gray-900 mb-4">Monthly Pitch Trends</h5>
                                        <div class="space-y-3">
                                            ${generateMonthlyPitchTrends(employee).map(month => `
                                                <div class="flex justify-between items-center">
                                                    <span class="text-sm text-gray-600">${month.month}</span>
                                                    <div class="flex items-center space-x-2">
                                                        <div class="w-20 bg-gray-200 rounded-full h-2">
                                                            <div class="bg-blue-500 h-2 rounded-full" style="width: ${month.percentage}%"></div>
                                                        </div>
                                                        <span class="text-sm font-medium">${month.pitches}</span>
                                                    </div>
                                                </div>
                                            `).join('')}
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Committee Participation</h4>
                            <div class="bg-white border border-gray-200 rounded-lg p-6">
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                                    <div>
                                        <h5 class="font-semibold text-gray-900 mb-3">Committee Details</h5>
                                        <div class="space-y-2">
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Primary Committee:</span>
                                                <span class="font-medium">${getEmployeeCommittee(employee.department)}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Committee Role:</span>
                                                <span class="font-medium">${getCommitteeRole(employee.performance)}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Meetings Attended:</span>
                                                <span class="font-medium">${generateCommitteeStats(employee).meetingsAttended}/12</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Contributions:</span>
                                                <span class="font-medium">${generateCommitteeStats(employee).contributions}</span>
                                            </div>
                                        </div>
                                    </div>
                                    <div>
                                        <h5 class="font-semibold text-gray-900 mb-3">Committee Performance</h5>
                                        <div class="space-y-2">
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Attendance Rate:</span>
                                                <span class="font-medium text-green-600">${generateCommitteeStats(employee).attendanceRate}%</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Initiative Score:</span>
                                                <span class="font-medium text-blue-600">${generateCommitteeStats(employee).initiativeScore}/10</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Leadership Rating:</span>
                                                <span class="font-medium text-purple-600">${generateCommitteeStats(employee).leadershipRating}/5</span>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- MIS & Forms Tab -->
                <div id="forms-tab" class="report-content hidden">
                    <div class="space-y-8">
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">MIS Form Submission Summary</h4>
                            <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                                <div class="bg-blue-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-blue-600">${generateMISStats(employee).monthlyForms}</div>
                                    <div class="text-sm text-gray-600">This Month</div>
                                </div>
                                <div class="bg-green-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-green-600">${generateMISStats(employee).totalForms}</div>
                                    <div class="text-sm text-gray-600">Total Till Date</div>
                                </div>
                                <div class="bg-purple-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-purple-600">${generateMISStats(employee).avgMonthly}</div>
                                    <div class="text-sm text-gray-600">Avg Monthly</div>
                                </div>
                                <div class="bg-orange-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-orange-600">${generateMISStats(employee).completionRate}%</div>
                                    <div class="text-sm text-gray-600">Completion Rate</div>
                                </div>
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Form Type Breakdown</h4>
                            <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                                ${generateFormBreakdown(employee).map(form => `
                                    <div class="bg-white border border-gray-200 rounded-lg p-6">
                                        <div class="flex items-center justify-between mb-4">
                                            <h5 class="font-semibold text-gray-900">${form.type}</h5>
                                            <span class="text-2xl">${form.icon}</span>
                                        </div>
                                        <div class="space-y-2">
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Submitted:</span>
                                                <span class="font-medium">${form.submitted}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Approved:</span>
                                                <span class="font-medium text-green-600">${form.approved}</span>
                                            </div>
                                            <div class="flex justify-between">
                                                <span class="text-gray-600">Pending:</span>
                                                <span class="font-medium text-orange-600">${form.pending}</span>
                                            </div>
                                        </div>
                                        <div class="mt-4">
                                            <div class="w-full bg-gray-200 rounded-full h-2">
                                                <div class="bg-green-500 h-2 rounded-full" style="width: ${form.approvalRate}%"></div>
                                            </div>
                                            <div class="text-xs text-gray-500 mt-1">${form.approvalRate}% Approval Rate</div>
                                        </div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Monthly Form Submission Trends</h4>
                            <div class="bg-white border border-gray-200 rounded-lg p-6">
                                <div class="space-y-4">
                                    ${generateMonthlyFormTrends(employee).map(month => `
                                        <div class="flex items-center justify-between">
                                            <div class="flex items-center space-x-4">
                                                <span class="text-sm font-medium text-gray-900 w-16">${month.month}</span>
                                                <div class="w-40 bg-gray-200 rounded-full h-3">
                                                    <div class="bg-blue-500 h-3 rounded-full" style="width: ${month.percentage}%"></div>
                                                </div>
                                            </div>
                                            <div class="flex space-x-4 text-sm">
                                                <span class="text-gray-600">Forms: <strong>${month.forms}</strong></span>
                                                <span class="text-green-600">Approved: <strong>${month.approved}</strong></span>
                                            </div>
                                        </div>
                                    `).join('')}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Uploads & Links Tab -->
                <div id="uploads-tab" class="report-content hidden">
                    <div class="space-y-8">
                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Work Uploads & Documentation</h4>
                            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                                <div class="bg-blue-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-blue-600">${generateUploadStats(employee).totalUploads}</div>
                                    <div class="text-sm text-gray-600">Total Uploads</div>
                                </div>
                                <div class="bg-green-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-green-600">${generateUploadStats(employee).totalLinks}</div>
                                    <div class="text-sm text-gray-600">Shared Links</div>
                                </div>
                                <div class="bg-purple-50 p-4 rounded-lg text-center">
                                    <div class="text-2xl font-bold text-purple-600">${generateUploadStats(employee).totalSize}</div>
                                    <div class="text-sm text-gray-600">Total Size</div>
                                </div>
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Recent Uploads</h4>
                            <div class="space-y-4">
                                ${generateRecentUploads(employee).map(upload => `
                                    <div class="bg-white border border-gray-200 rounded-lg p-4">
                                        <div class="flex items-center justify-between">
                                            <div class="flex items-center space-x-3">
                                                <div class="text-2xl">${upload.icon}</div>
                                                <div>
                                                    <h5 class="font-medium text-gray-900">${upload.name}</h5>
                                                    <p class="text-sm text-gray-600">${upload.description}</p>
                                                </div>
                                            </div>
                                            <div class="text-right">
                                                <div class="text-sm text-gray-500">${upload.date}</div>
                                                <div class="text-xs text-gray-400">${upload.size}</div>
                                            </div>
                                        </div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">Shared Links & Resources</h4>
                            <div class="space-y-4">
                                ${generateSharedLinks(employee).map(link => `
                                    <div class="bg-white border border-gray-200 rounded-lg p-4">
                                        <div class="flex items-center justify-between">
                                            <div class="flex items-center space-x-3">
                                                <div class="text-2xl">🔗</div>
                                                <div>
                                                    <h5 class="font-medium text-gray-900">${link.title}</h5>
                                                    <p class="text-sm text-blue-600 hover:underline cursor-pointer">${link.url}</p>
                                                    <p class="text-xs text-gray-600">${link.description}</p>
                                                </div>
                                            </div>
                                            <div class="text-right">
                                                <div class="text-sm text-gray-500">${link.date}</div>
                                                <div class="text-xs text-gray-400">${link.category}</div>
                                            </div>
                                        </div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>

                        <div>
                            <h4 class="text-lg font-semibold text-gray-900 mb-6">File Type Distribution</h4>
                            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                                ${generateFileTypeStats(employee).map(type => `
                                    <div class="bg-white border border-gray-200 rounded-lg p-4 text-center">
                                        <div class="text-2xl mb-2">${type.icon}</div>
                                        <div class="text-lg font-bold text-gray-900">${type.count}</div>
                                        <div class="text-sm text-gray-600">${type.type}</div>
                                        <div class="text-xs text-gray-500">${type.size}</div>
                                    </div>
                                `).join('')}
                            </div>
                        </div>
                    </div>
                </div>
            `;
            
            document.getElementById('employeeModal').classList.remove('hidden');
        }

        function showReportTab(tabName) {
            // Hide all report content
            document.querySelectorAll('.report-content').forEach(content => {
                content.classList.add('hidden');
            });
            
            // Show selected tab content
            document.getElementById(tabName + '-tab').classList.remove('hidden');
            
            // Update tab styling
            document.querySelectorAll('.report-tab').forEach(tab => {
                tab.classList.remove('active-report-tab');
                tab.classList.add('text-gray-500');
            });
            event.target.classList.add('active-report-tab');
            event.target.classList.remove('text-gray-500');
        }

        function closeEmployeeModal() {
            document.getElementById('employeeModal').classList.add('hidden');
        }

        function calculateTenure(joinDate) {
            const join = new Date(joinDate);
            const now = new Date();
            const diffTime = Math.abs(now - join);
            const diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
            
            if (diffDays < 30) return `${diffDays} days`;
            if (diffDays < 365) return `${Math.floor(diffDays / 30)} months`;
            return `${Math.floor(diffDays / 365)} years`;
        }

        function initializeCharts() {
            // Department Performance Chart
            const deptCtx = document.getElementById('departmentChart').getContext('2d');
            new Chart(deptCtx, {
                type: 'doughnut',
                data: {
                    labels: ['Business Dev', 'Technology', 'Marketing', 'Finance', 'HR', 'Operations'],
                    datasets: [{
                        data: [8, 3, 2, 1, 1, 1],
                        backgroundColor: ['#3b82f6', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6', '#06b6d4']
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom' }
                    }
                }
            });

            // Performance Trends Chart
            const trendsCtx = document.getElementById('trendsChart').getContext('2d');
            new Chart(trendsCtx, {
                type: 'line',
                data: {
                    labels: ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun'],
                    datasets: [{
                        label: 'Average Performance',
                        data: [82, 85, 87, 86, 89, 87.5],
                        borderColor: '#3b82f6',
                        backgroundColor: 'rgba(59, 130, 246, 0.1)',
                        tension: 0.4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: false, min: 75, max: 95 }
                    }
                }
            });

            // Task Completion Chart
            const taskCtx = document.getElementById('taskCompletionChart').getContext('2d');
            new Chart(taskCtx, {
                type: 'bar',
                data: {
                    labels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat'],
                    datasets: [{
                        label: 'Tasks Completed',
                        data: [45, 52, 48, 61, 55, 32],
                        backgroundColor: '#10b981'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true }
                    }
                }
            });
        }

        function updateActivitiesFeed() {
            const activities = [
                { time: '2 min ago', user: 'Riya Sharma', action: 'submitted Management Committee Form', type: 'form' },
                { time: '5 min ago', user: 'Ankit Verma', action: 'updated existing deal status', type: 'deal' },
                { time: '12 min ago', user: 'Priya Patel', action: 'completed marketing campaign analysis', type: 'task' },
                { time: '18 min ago', user: 'Rahul Singh', action: 'deployed new feature to production', type: 'deployment' },
                { time: '25 min ago', user: 'Sneha Gupta', action: 'conducted team performance review', type: 'review' },
                { time: '32 min ago', user: 'Amit Kumar', action: 'generated monthly financial report', type: 'report' },
                { time: '45 min ago', user: 'Kavya Reddy', action: 'optimized operational workflow', type: 'process' },
                { time: '1 hour ago', user: 'Arjun Mehta', action: 'completed lead generation training', type: 'training' }
            ];

            const feed = document.getElementById('activitiesFeed');
            feed.innerHTML = activities.map(activity => {
                const iconMap = {
                    form: '📋', deal: '💼', task: '✅', deployment: '🚀',
                    review: '📊', report: '📈', process: '⚙️', training: '🎓'
                };
                
                return `
                    <div class="flex items-center space-x-4 p-4 bg-gray-50 rounded-lg">
                        <div class="text-2xl">${iconMap[activity.type]}</div>
                        <div class="flex-1">
                            <div class="text-sm text-gray-900">
                                <span class="font-semibold">${activity.user}</span> ${activity.action}
                            </div>
                            <div class="text-xs text-gray-500">${activity.time}</div>
                        </div>
                    </div>
                `;
            }).join('');
        }

        function updateRealTimeData() {
            // Update last updated time
            document.getElementById('lastUpdated').textContent = new Date().toLocaleTimeString();
            
            // Simulate real-time updates
            const currentForms = parseInt(document.getElementById('formsSubmitted').textContent);
            if (Math.random() > 0.7) { // 30% chance of new form
                document.getElementById('formsSubmitted').textContent = currentForms + 1;
            }
            
            // Update active users count
            const activeCount = Math.floor(Math.random() * 5) + 24;
            document.getElementById('activeUsers').textContent = `${activeCount}/28`;
        }

        function exportData() {
            // Simulate data export
            const button = event.target;
            const originalText = button.textContent;
            
            button.textContent = '📊 Exporting...';
            button.disabled = true;
            
            setTimeout(() => {
                button.textContent = '✅ Exported!';
                setTimeout(() => {
                    button.textContent = originalText;
                    button.disabled = false;
                }, 2000);
            }, 2000);
        }

        function generateReport() {
            // Simulate report generation
            const button = event.target;
            const originalText = button.textContent;
            
            button.textContent = '📋 Generating...';
            button.disabled = true;
            
            setTimeout(() => {
                button.textContent = '✅ Report Ready!';
                setTimeout(() => {
                    button.textContent = originalText;
                    button.disabled = false;
                }, 2000);
            }, 3000);
        }

        // Close modal when clicking outside
        document.getElementById('employeeModal').addEventListener('click', function(e) {
            if (e.target === this) {
                closeEmployeeModal();
            }
        });
    </script>
<script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9730f34807e6a6b9',t:'MTc1NTg1MDgyMC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>

