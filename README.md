<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dev Diary</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.10/firebase-firestore-compat.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, sans-serif;
        }

        :root {
            --primary-bg: #ffffff;
            --secondary-bg: #f8f9fa;
            --card-bg: #ffffff;
            --text-primary: #1a1a1a;
            --text-secondary: #666666;
            --accent: #3a86ff;
            --accent-light: #e6f0ff;
            --border: #e0e0e0;
            --shadow: 0 2px 12px rgba(0, 0, 0, 0.08);
            --radius: 12px;
            --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        /* Dark Theme Variables */
        .dark-theme {
            --primary-bg: #0f1419;
            --secondary-bg: #1a1f2e;
            --card-bg: #1e2536;
            --text-primary: #ffffff;
            --text-secondary: #a0a0a0;
            --accent: #4cc9f0;
            --accent-light: rgba(76, 201, 240, 0.1);
            --border: #2d3748;
            --shadow: 0 2px 12px rgba(0, 0, 0, 0.3);
        }

        /* Minimal Theme Variables */
        .minimal-theme {
            --primary-bg: #fefefe;
            --secondary-bg: #f9f9f9;
            --card-bg: #ffffff;
            --text-primary: #222222;
            --text-secondary: #777777;
            --accent: #5d5d5d;
            --accent-light: #f0f0f0;
            --border: #e8e8e8;
            --shadow: 0 1px 6px rgba(0, 0, 0, 0.05);
        }

        body {
            background-color: var(--primary-bg);
            color: var(--text-primary);
            transition: var(--transition);
            min-height: 100vh;
            overflow-x: hidden;
        }

        /* Slide Menu Styles */
        .slide-menu {
            position: fixed;
            left: -320px;
            top: 0;
            width: 300px;
            height: 100vh;
            background: var(--card-bg);
            box-shadow: 5px 0 25px rgba(0, 0, 0, 0.1);
            z-index: 1000;
            transition: var(--transition);
            padding: 30px 20px;
            overflow-y: auto;
            border-right: 1px solid var(--border);
        }

        .slide-menu.open {
            left: 0;
        }

        .menu-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 40px;
            padding-bottom: 20px;
            border-bottom: 1px solid var(--border);
        }

        .menu-title {
            font-size: 1.5rem;
            font-weight: 700;
            color: var(--accent);
        }

        .close-menu {
            background: none;
            border: none;
            color: var(--text-secondary);
            font-size: 1.3rem;
            cursor: pointer;
            padding: 8px;
            border-radius: 8px;
            transition: var(--transition);
        }

        .close-menu:hover {
            background: var(--accent-light);
            color: var(--accent);
        }

        .menu-section {
            margin-bottom: 30px;
        }

        .section-title {
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--text-secondary);
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .section-title i {
            font-size: 1rem;
        }

        .menu-item {
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 12px 16px;
            color: var(--text-primary);
            text-decoration: none;
            border-radius: 10px;
            margin-bottom: 8px;
            transition: var(--transition);
            cursor: pointer;
            border: 1px solid transparent;
        }

        .menu-item:hover {
            background: var(--accent-light);
            transform: translateX(5px);
        }

        .menu-item.active {
            background: var(--accent);
            color: white;
            border-color: var(--accent);
        }

        .menu-item i {
            width: 20px;
            text-align: center;
            font-size: 1.1rem;
        }

        .user-info {
            padding: 20px;
            background: var(--secondary-bg);
            border-radius: var(--radius);
            margin-top: 30px;
            text-align: center;
        }

        .user-avatar {
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: linear-gradient(135deg, var(--accent), #6c5ce7);
            margin: 0 auto 15px;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 1.5rem;
            font-weight: 600;
            border: 3px solid var(--card-bg);
            box-shadow: var(--shadow);
        }

        .user-name {
            font-weight: 600;
            margin-bottom: 5px;
        }

        .user-email {
            font-size: 0.9rem;
            color: var(--text-secondary);
        }

        /* Theme Options */
        .theme-options {
            display: flex;
            gap: 10px;
            margin-top: 10px;
        }

        .theme-option {
            flex: 1;
            padding: 10px;
            border-radius: 8px;
            border: 2px solid var(--border);
            background: var(--secondary-bg);
            cursor: pointer;
            transition: var(--transition);
            text-align: center;
            font-size: 0.85rem;
        }

        .theme-option:hover {
            border-color: var(--accent);
        }

        .theme-option.active {
            border-color: var(--accent);
            background: var(--accent-light);
        }

        .theme-light { background: #ffffff; color: #222; }
        .theme-dark { background: #1e2536; color: white; }
        .theme-minimal { background: #fefefe; color: #222; }

        /* Main Header */
        .main-header {
            position: sticky;
            top: 0;
            z-index: 100;
            background: var(--primary-bg);
            border-bottom: 1px solid var(--border);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            backdrop-filter: blur(10px);
        }

        .header-left {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .menu-toggle {
            background: none;
            border: none;
            color: var(--text-primary);
            font-size: 1.5rem;
            cursor: pointer;
            padding: 10px;
            border-radius: 10px;
            transition: var(--transition);
        }

        .menu-toggle:hover {
            background: var(--accent-light);
            color: var(--accent);
        }

        .app-title {
            font-size: 1.4rem;
            font-weight: 700;
            background: linear-gradient(135deg, var(--accent), #6c5ce7);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .header-actions {
            display: flex;
            align-items: center;
            gap: 15px;
        }

        .icon-btn {
            background: none;
            border: none;
            color: var(--text-secondary);
            font-size: 1.2rem;
            cursor: pointer;
            padding: 10px;
            border-radius: 10px;
            transition: var(--transition);
        }

        .icon-btn:hover {
            background: var(--accent-light);
            color: var(--accent);
        }

        /* Main Content */
        .main-content {
            padding: 20px;
            max-width: 800px;
            margin: 0 auto;
        }

        /* Add Note Section - Hidden by default */
        .add-note-section {
            background: var(--card-bg);
            border-radius: var(--radius);
            padding: 25px;
            margin-bottom: 30px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            display: none; /* Initially hidden */
        }

        .add-note-section.visible {
            display: block;
            animation: slideDown 0.3s ease;
        }

        @keyframes slideDown {
            from {
                opacity: 0;
                transform: translateY(-20px);
            }
            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        .form-title {
            font-size: 1.2rem;
            font-weight: 600;
            margin-bottom: 20px;
            color: var(--accent);
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .form-group {
            margin-bottom: 20px;
        }

        .form-label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
            color: var(--text-primary);
        }

        .form-input {
            width: 100%;
            padding: 14px 16px;
            border: 1px solid var(--border);
            border-radius: 10px;
            font-size: 1rem;
            background: var(--secondary-bg);
            color: var(--text-primary);
            transition: var(--transition);
        }

        .form-input:focus {
            outline: none;
            border-color: var(--accent);
            box-shadow: 0 0 0 3px rgba(58, 134, 255, 0.1);
        }

        .form-input::placeholder {
            color: var(--text-secondary);
            opacity: 0.7;
        }

        .tags-container {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
            margin-top: 10px;
        }

        .tag-option {
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 0.9rem;
            cursor: pointer;
            transition: var(--transition);
            border: 2px solid var(--border);
            background: var(--secondary-bg);
            color: var(--text-secondary);
        }

        .tag-option:hover {
            transform: translateY(-2px);
            border-color: var(--accent);
        }

        .tag-option.selected {
            background: var(--accent);
            color: white;
            border-color: var(--accent);
        }

        .editor-toolbar {
            display: flex;
            gap: 8px;
            margin-bottom: 10px;
            flex-wrap: wrap;
            padding: 10px;
            background: var(--secondary-bg);
            border-radius: 10px;
            border: 1px solid var(--border);
        }

        .format-btn {
            background: var(--card-bg);
            border: 1px solid var(--border);
            color: var(--text-primary);
            padding: 8px 12px;
            border-radius: 8px;
            cursor: pointer;
            transition: var(--transition);
            font-size: 0.9rem;
        }

        .format-btn:hover {
            background: var(--accent);
            color: white;
            border-color: var(--accent);
        }

        .mic-btn {
            margin-left: auto;
            background: var(--card-bg);
            border: 1px solid var(--border);
            color: var(--text-primary);
            padding: 8px 12px;
            border-radius: 8px;
            cursor: pointer;
            transition: var(--transition);
        }

        .mic-btn:hover {
            background: var(--accent);
            color: white;
            border-color: var(--accent);
        }

        .mic-btn.recording {
            background: #ff4757;
            color: white;
            border-color: #ff4757;
            animation: pulse 1.5s infinite;
        }

        @keyframes pulse {
            0% { box-shadow: 0 0 0 0 rgba(255, 71, 87, 0.7); }
            70% { box-shadow: 0 0 0 10px rgba(255, 71, 87, 0); }
            100% { box-shadow: 0 0 0 0 rgba(255, 71, 87, 0); }
        }

        .form-actions {
            display: flex;
            gap: 10px;
            margin-top: 20px;
        }

        .btn {
            padding: 12px 24px;
            border: none;
            border-radius: 10px;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-primary {
            background: var(--accent);
            color: white;
        }

        .btn-primary:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(58, 134, 255, 0.3);
        }

        .btn-secondary {
            background: var(--secondary-bg);
            color: var(--text-primary);
            border: 1px solid var(--border);
        }

        .btn-secondary:hover {
            background: var(--accent-light);
            border-color: var(--accent);
        }

        /* Notes Section */
        .notes-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 25px;
        }

        .notes-title {
            font-size: 1.3rem;
            font-weight: 600;
            color: var(--text-primary);
        }

        .notes-actions {
            display: flex;
            gap: 10px;
        }

        .notes-container {
            display: grid;
            gap: 20px;
        }

        .note-card {
            background: var(--card-bg);
            border-radius: var(--radius);
            padding: 25px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
            transition: var(--transition);
            animation: fadeIn 0.4s ease;
            position: relative;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }

        .note-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
        }

        .note-header {
            display: flex;
            justify-content: space-between;
            align-items: flex-start;
            margin-bottom: 15px;
        }

        .note-title {
            font-size: 1.2rem;
            font-weight: 600;
            color: var(--text-primary);
            line-height: 1.4;
        }

        .note-date {
            font-size: 0.85rem;
            color: var(--text-secondary);
            white-space: nowrap;
            margin-left: 15px;
        }

        .note-description {
            color: var(--text-secondary);
            line-height: 1.6;
            margin-bottom: 15px;
            white-space: pre-wrap;
        }

        .note-tags {
            display: flex;
            gap: 8px;
            flex-wrap: wrap;
            margin-bottom: 15px;
        }

        .note-tag {
            padding: 4px 12px;
            border-radius: 12px;
            font-size: 0.8rem;
            font-weight: 500;
            background: var(--accent-light);
            color: var(--accent);
            border: 1px solid var(--accent-light);
        }

        .note-actions {
            display: flex;
            justify-content: flex-end;
            gap: 10px;
            margin-top: 15px;
            padding-top: 15px;
            border-top: 1px solid var(--border);
        }

        .action-btn {
            background: none;
            border: none;
            width: 40px;
            height: 40px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: var(--transition);
            color: var(--text-secondary);
        }

        .action-btn:hover {
            background: var(--accent-light);
            color: var(--accent);
            transform: scale(1.1);
        }

        /* Edit Mode */
        .note-card.edit-mode {
            border-color: var(--accent);
        }

        .note-title-input,
        .note-description-input {
            width: 100%;
            padding: 12px 16px;
            border: 1px solid var(--border);
            border-radius: 10px;
            font-size: 1rem;
            background: var(--secondary-bg);
            color: var(--text-primary);
            margin-bottom: 15px;
            transition: var(--transition);
        }

        .note-title-input:focus,
        .note-description-input:focus {
            outline: none;
            border-color: var(--accent);
        }

        .note-title-input {
            font-size: 1.2rem;
            font-weight: 600;
        }

        .note-description-input {
            min-height: 120px;
            resize: vertical;
        }

        /* Empty State */
        .empty-state {
            text-align: center;
            padding: 60px 20px;
            color: var(--text-secondary);
        }

        .empty-icon {
            font-size: 3.5rem;
            margin-bottom: 20px;
            color: var(--border);
        }

        .empty-title {
            font-size: 1.2rem;
            margin-bottom: 10px;
            color: var(--text-primary);
        }

        /* Loading State */
        .loading {
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 60px;
        }

        .spinner {
            width: 40px;
            height: 40px;
            border: 3px solid var(--accent-light);
            border-top: 3px solid var(--accent);
            border-radius: 50%;
            animation: spin 1s linear infinite;
        }

        @keyframes spin {
            0% { transform: rotate(0deg); }
            100% { transform: rotate(360deg); }
        }

        /* Alert */
        .alert {
            position: fixed;
            top: 20px;
            right: 20px;
            background: var(--card-bg);
            border-left: 4px solid var(--accent);
            padding: 15px 20px;
            border-radius: 10px;
            box-shadow: var(--shadow);
            z-index: 1001;
            animation: slideInRight 0.3s ease;
            max-width: 350px;
            border: 1px solid var(--border);
        }

        @keyframes slideInRight {
            from {
                opacity: 0;
                transform: translateX(100%);
            }
            to {
                opacity: 1;
                transform: translateX(0);
            }
        }

        .alert.success {
            border-left-color: #00b894;
        }

        .alert.error {
            border-left-color: #ff4757;
        }

        /* Search Bar */
        .search-bar {
            position: relative;
            margin-bottom: 25px;
        }

        .search-input {
            width: 100%;
            padding: 14px 16px 14px 48px;
            border: 1px solid var(--border);
            border-radius: 10px;
            font-size: 1rem;
            background: var(--secondary-bg);
            color: var(--text-primary);
            transition: var(--transition);
        }

        .search-input:focus {
            outline: none;
            border-color: var(--accent);
            box-shadow: 0 0 0 3px rgba(58, 134, 255, 0.1);
        }

        .search-icon {
            position: absolute;
            left: 16px;
            top: 50%;
            transform: translateY(-50%);
            color: var(--text-secondary);
        }

        /* Floating Action Button */
        .fab {
            position: fixed;
            bottom: 30px;
            right: 30px;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: var(--accent);
            color: white;
            border: none;
            font-size: 1.5rem;
            cursor: pointer;
            box-shadow: 0 4px 20px rgba(58, 134, 255, 0.3);
            transition: var(--transition);
            z-index: 100;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        .fab:hover {
            transform: scale(1.1);
            box-shadow: 0 6px 25px rgba(58, 134, 255, 0.4);
        }

        /* Auth Pages */
        .auth-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            padding: 20px;
            background: var(--primary-bg);
        }

        .auth-card {
            background: var(--card-bg);
            border-radius: var(--radius);
            padding: 40px;
            width: 100%;
            max-width: 400px;
            box-shadow: var(--shadow);
            border: 1px solid var(--border);
        }

        .auth-title {
            font-size: 1.8rem;
            font-weight: 700;
            margin-bottom: 10px;
            text-align: center;
            background: linear-gradient(135deg, var(--accent), #6c5ce7);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .auth-subtitle {
            color: var(--text-secondary);
            text-align: center;
            margin-bottom: 30px;
        }

        /* Google Button Styling */
        .google-btn {
            background: #4285F4;
            color: white;
            border: none;
            border-radius: 10px;
            padding: 12px 24px;
            font-weight: 600;
            cursor: pointer;
            transition: var(--transition);
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            width: 100%;
            margin-bottom: 15px;
        }

        .google-btn:hover {
            background: #357ae8;
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(66, 133, 244, 0.3);
        }

        .google-btn i {
            font-size: 1.2rem;
        }

        /* Responsive */
        @media (max-width: 768px) {
            .main-content {
                padding: 15px;
            }
            
            .note-card {
                padding: 20px;
            }
            
            .slide-menu {
                width: 280px;
                left: -280px;
            }
            
            .fab {
                bottom: 20px;
                right: 20px;
            }
            
            .alert {
                left: 20px;
                right: 20px;
                max-width: calc(100% - 40px);
            }
        }

        @media (max-width: 480px) {
            .auth-card {
                padding: 30px 20px;
            }
            
            .header-actions {
                display: none;
            }
        }

        /* Overlay for menu */
        .menu-overlay {
            position: fixed;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.5);
            z-index: 999;
            opacity: 0;
            visibility: hidden;
            transition: var(--transition);
            backdrop-filter: blur(3px);
        }

        .menu-overlay.visible {
            opacity: 1;
            visibility: visible;
        }
    </style>
</head>
<body class="light-theme">
    <!-- Slide Menu -->
    <div class="slide-menu" id="slideMenu">
        <div class="menu-header">
            <h2 class="menu-title">Dev Diary</h2>
            <button class="close-menu" id="closeMenu">
                <i class="fas fa-times"></i>
            </button>
        </div>
        
        <div class="menu-section">
            <div class="section-title">
                <i class="fas fa-user-circle"></i>
                Profile
            </div>
            <div class="user-info" id="menuUserInfo" style="display: none;">
                <div class="user-avatar" id="menuUserAvatar">U</div>
                <div class="user-name" id="menuUserName">User Name</div>
                <div class="user-email" id="menuUserEmail">user@example.com</div>
            </div>
        </div>
        
        <div class="menu-section">
            <div class="section-title">
                <i class="fas fa-folder"></i>
                Categories
            </div>
            <div class="menu-item active" data-category="all">
                <i class="fas fa-sticky-note"></i>
                <span>All Notes</span>
            </div>
            <div class="menu-item" data-category="work">
                <i class="fas fa-briefcase"></i>
                <span>Work</span>
            </div>
            <div class="menu-item" data-category="personal">
                <i class="fas fa-user"></i>
                <span>Personal</span>
            </div>
            <div class="menu-item" data-category="ideas">
                <i class="fas fa-lightbulb"></i>
                <span>Ideas</span>
            </div>
            <div class="menu-item" data-category="important">
                <i class="fas fa-star"></i>
                <span>Important</span>
            </div>
        </div>
        
        <div class="menu-section">
            <div class="section-title">
                <i class="fas fa-palette"></i>
                Theme
            </div>
            <div class="theme-options">
                <div class="theme-option theme-light active" data-theme="light">
                    Light
                </div>
                <div class="theme-option theme-dark" data-theme="dark">
                    Dark
                </div>
                <div class="theme-option theme-minimal" data-theme="minimal">
                    Minimal
                </div>
            </div>
        </div>
        
        <div class="menu-section">
            <div class="section-title">
                <i class="fas fa-cog"></i>
                Settings
            </div>
            <div class="menu-item" id="menuLogout">
                <i class="fas fa-sign-out-alt"></i>
                <span>Logout</span>
            </div>
        </div>
    </div>
    
    <!-- Menu Overlay -->
    <div class="menu-overlay" id="menuOverlay"></div>
    
    <!-- Main Layout -->
    <div id="mainLayout" style="display: none;">
        <!-- Header -->
        <header class="main-header">
            <div class="header-left">
                <button class="menu-toggle" id="menuToggle">
                    <i class="fas fa-bars"></i>
                </button>
                <h1 class="app-title">Dev Diary</h1>
            </div>
            <div class="header-actions">
                <button class="icon-btn" id="searchToggle">
                    <i class="fas fa-search"></i>
                </button>
                <button class="icon-btn" id="addNoteToggle">
                    <i class="fas fa-plus"></i>
                </button>
            </div>
        </header>
        
        <!-- Main Content -->
        <main class="main-content">
            <!-- Search Bar -->
            <div class="search-bar" id="searchBar" style="display: none;">
                <i class="fas fa-search search-icon"></i>
                <input type="text" class="search-input" id="searchInput" placeholder="Search notes...">
            </div>
            
            <!-- Add Note Section -->
            <div class="add-note-section" id="addNoteSection">
                <h2 class="form-title">
                    <i class="fas fa-plus-circle"></i>
                    Add New Note
                </h2>
                <div class="form-group">
                    <label for="note-title" class="form-label">Title</label>
                    <input type="text" id="note-title" class="form-input" placeholder="Enter note title">
                </div>
                
                <div class="form-group">
                    <label class="form-label">Tags</label>
                    <div class="tags-container">
                        <div class="tag-option" data-tag="work">Work</div>
                        <div class="tag-option" data-tag="personal">Personal</div>
                        <div class="tag-option" data-tag="ideas">Ideas</div>
                        <div class="tag-option" data-tag="important">Important</div>
                    </div>
                </div>
                
                <div class="form-group">
                    <label for="note-description" class="form-label">Description</label>
                    <div class="editor-toolbar">
                        <button class="format-btn" data-command="bold">
                            <i class="fas fa-bold"></i>
                        </button>
                        <button class="format-btn" data-command="italic">
                            <i class="fas fa-italic"></i>
                        </button>
                        <button class="format-btn" data-command="underline">
                            <i class="fas fa-underline"></i>
                        </button>
                        <button class="mic-btn" id="micBtn">
                            <i class="fas fa-microphone"></i>
                        </button>
                    </div>
                    <textarea id="note-description" class="form-input" rows="4" 
                              placeholder="Enter note description or use the mic to speak..."></textarea>
                </div>
                
                <div class="form-actions">
                    <button class="btn btn-primary" id="saveNoteBtn">
                        <i class="fas fa-save"></i>
                        Save Note
                    </button>
                    <button class="btn btn-secondary" id="cancelNoteBtn">
                        <i class="fas fa-times"></i>
                        Cancel
                    </button>
                </div>
            </div>
            
            <!-- Notes Section -->
            <div class="notes-header">
                <h2 class="notes-title">My Notes</h2>
                <div class="notes-actions">
                    <button class="icon-btn" id="filterToggle">
                        <i class="fas fa-filter"></i>
                    </button>
                </div>
            </div>
            
            <div id="notesList" class="notes-container">
                <!-- Notes will be loaded here -->
            </div>
        </main>
        
        <!-- Floating Action Button -->
        <button class="fab" id="fabAddNote">
            <i class="fas fa-plus"></i>
        </button>
    </div>
    
    <!-- Auth Pages Container -->
    <div id="authContainer">
        <!-- Login Page will be loaded here -->
    </div>

    <!-- Firebase Configuration -->
    <script>
        // Firebase configuration - UPDATED WITH WORKING DOMAINS
        const firebaseConfig = {
            apiKey: "AIzaSyB_8SLzdD6T5lmHNDycsjL5ow8I1-eNXUw",
            authDomain: "devdiary-fff36.firebaseapp.com",
            databaseURL: "https://devdiary-fff36-default-rtdb.firebaseio.com",
            projectId: "devdiary-fff36",
            storageBucket: "devdiary-fff36.firebasestorage.app",
            messagingSenderId: "349653902749",
            appId: "1:349653902749:web:b978d9bfa583492801bb74"
        };

        // Initialize Firebase
        let auth, db;
        try {
            firebase.initializeApp(firebaseConfig);
            auth = firebase.auth();
            db = firebase.firestore();
            
            // Enable offline persistence
            db.enablePersistence()
              .catch((err) => {
                  console.warn("Offline persistence failed:", err);
              });
              
            console.log('Firebase initialized successfully');
        } catch (error) {
            console.error('Firebase initialization error:', error);
        }

        // App State
        let currentUser = null;
        let notes = [];
        let selectedTags = [];
        let currentCategory = 'all';
        let searchQuery = '';
        let notesListener = null;
        let isRecording = false;

        // DOM Elements
        const slideMenu = document.getElementById('slideMenu');
        const menuToggle = document.getElementById('menuToggle');
        const closeMenu = document.getElementById('closeMenu');
        const menuOverlay = document.getElementById('menuOverlay');
        const mainLayout = document.getElementById('mainLayout');
        const authContainer = document.getElementById('authContainer');
        const addNoteSection = document.getElementById('addNoteSection');
        const fabAddNote = document.getElementById('fabAddNote');
        const addNoteToggle = document.getElementById('addNoteToggle');
        const cancelNoteBtn = document.getElementById('cancelNoteBtn');
        const saveNoteBtn = document.getElementById('saveNoteBtn');
        const searchToggle = document.getElementById('searchToggle');
        const searchBar = document.getElementById('searchBar');
        const searchInput = document.getElementById('searchInput');
        const notesList = document.getElementById('notesList');
        const tagOptions = document.querySelectorAll('.tag-option');
        const formatButtons = document.querySelectorAll('.format-btn');
        const micBtn = document.getElementById('micBtn');
        const menuItems = document.querySelectorAll('.menu-item[data-category]');
        const themeOptions = document.querySelectorAll('.theme-option');
        const menuLogout = document.getElementById('menuLogout');
        const menuUserInfo = document.getElementById('menuUserInfo');
        const menuUserAvatar = document.getElementById('menuUserAvatar');
        const menuUserName = document.getElementById('menuUserName');
        const menuUserEmail = document.getElementById('menuUserEmail');

        // Show alert
        function showAlert(message, type = 'info') {
            const alert = document.createElement('div');
            alert.className = `alert ${type}`;
            alert.innerHTML = `
                <div style="display: flex; align-items: center; gap: 10px;">
                    <i class="fas fa-${type === 'success' ? 'check-circle' : type === 'error' ? 'exclamation-circle' : 'info-circle'}"></i>
                    <span>${message}</span>
                </div>
            `;
            
            document.body.appendChild(alert);
            
            setTimeout(() => {
                alert.remove();
            }, 3000);
        }

        // Load login page
        function loadLoginPage() {
            authContainer.innerHTML = `
                <div class="auth-container">
                    <div class="auth-card">
                        <h1 class="auth-title">Dev Diary</h1>
                        <p class="auth-subtitle">Your personal development journal</p>
                        
                        <div class="form-group">
                            <label for="loginEmail" class="form-label">Email</label>
                            <input type="email" id="loginEmail" class="form-input" placeholder="Enter your email">
                        </div>
                        
                        <div class="form-group">
                            <label for="loginPassword" class="form-label">Password</label>
                            <input type="password" id="loginPassword" class="form-input" placeholder="Enter your password">
                        </div>
                        
                        <button class="btn btn-primary" id="loginBtn" style="width: 100%; margin-bottom: 15px;">
                            <i class="fas fa-sign-in-alt"></i>
                            Sign In
                        </button>
                        
                        <button class="google-btn" id="googleLoginBtn">
                            <i class="fab fa-google"></i>
                            Sign in with Google
                        </button>
                        
                        <div style="text-align: center; margin-top: 20px;">
                            <span style="color: var(--text-secondary);">Don't have an account? </span>
                            <button class="auth-link" id="showRegister" style="background: none; border: none; color: var(--accent); cursor: pointer; font-weight: 600;">
                                Create Account
                            </button>
                        </div>
                    </div>
                </div>
            `;
            
            // Add event listeners for login page
            document.getElementById('loginBtn')?.addEventListener('click', handleLogin);
            document.getElementById('googleLoginBtn')?.addEventListener('click', handleGoogleLogin);
            document.getElementById('showRegister')?.addEventListener('click', loadRegisterPage);
        }

        // Load register page
        function loadRegisterPage() {
            authContainer.innerHTML = `
                <div class="auth-container">
                    <div class="auth-card">
                        <h1 class="auth-title">Create Account</h1>
                        <p class="auth-subtitle">Join Dev Diary today</p>
                        
                        <div class="form-group">
                            <label for="registerName" class="form-label">Full Name</label>
                            <input type="text" id="registerName" class="form-input" placeholder="Enter your full name">
                        </div>
                        
                        <div class="form-group">
                            <label for="registerEmail" class="form-label">Email</label>
                            <input type="email" id="registerEmail" class="form-input" placeholder="Enter your email">
                        </div>
                        
                        <div class="form-group">
                            <label for="registerPassword" class="form-label">Password</label>
                            <input type="password" id="registerPassword" class="form-input" placeholder="Create a password">
                        </div>
                        
                        <div class="form-group">
                            <label for="registerConfirmPassword" class="form-label">Confirm Password</label>
                            <input type="password" id="registerConfirmPassword" class="form-input" placeholder="Confirm password">
                        </div>
                        
                        <button class="btn btn-primary" id="registerBtn" style="width: 100%; margin-bottom: 15px;">
                            <i class="fas fa-user-plus"></i>
                            Create Account
                        </button>
                        
                        <button class="google-btn" id="googleRegisterBtn">
                            <i class="fab fa-google"></i>
                            Sign up with Google
                        </button>
                        
                        <div style="text-align: center; margin-top: 20px;">
                            <span style="color: var(--text-secondary);">Already have an account? </span>
                            <button class="auth-link" id="showLogin" style="background: none; border: none; color: var(--accent); cursor: pointer; font-weight: 600;">
                                Sign In
                            </button>
                        </div>
                    </div>
                </div>
            `;
            
            // Add event listeners for register page
            document.getElementById('registerBtn')?.addEventListener('click', handleRegister);
            document.getElementById('googleRegisterBtn')?.addEventListener('click', handleGoogleLogin);
            document.getElementById('showLogin')?.addEventListener('click', loadLoginPage);
        }

        // Handle login
        function handleLogin() {
            const email = document.getElementById('loginEmail').value;
            const password = document.getElementById('loginPassword').value;
            
            if (!email || !password) {
                showAlert('Please enter email and password', 'error');
                return;
            }
            
            auth.signInWithEmailAndPassword(email, password)
                .then(() => {
                    showAlert('Login successful!', 'success');
                })
                .catch((error) => {
                    console.error('Login error:', error);
                    let errorMessage = 'Login failed. Please try again.';
                    
                    if (error.code === 'auth/user-not-found') {
                        errorMessage = 'No account found with this email.';
                    } else if (error.code === 'auth/wrong-password') {
                        errorMessage = 'Incorrect password.';
                    } else if (error.code === 'auth/invalid-email') {
                        errorMessage = 'Invalid email address.';
                    }
                    
                    showAlert(errorMessage, 'error');
                });
        }

        // Handle Google login - IMPROVED VERSION
        function handleGoogleLogin() {
            const provider = new firebase.auth.GoogleAuthProvider();
            
            // Add scopes
            provider.addScope('profile');
            provider.addScope('email');
            
            // Set custom parameters
            provider.setCustomParameters({
                prompt: 'select_account'
            });
            
            auth.signInWithPopup(provider)
                .then((result) => {
                    const user = result.user;
                    showAlert(`Welcome ${user.displayName}!`, 'success');
                })
                .catch((error) => {
                    console.error('Google login error:', error);
                    
                    let errorMessage = 'Google authentication failed. ';
                    
                    if (error.code === 'auth/popup-blocked') {
                        errorMessage += 'Popup was blocked by your browser. Please allow popups for this site.';
                    } else if (error.code === 'auth/popup-closed-by-user') {
                        errorMessage = 'Sign in was cancelled.';
                    } else if (error.code === 'auth/unauthorized-domain') {
                        errorMessage = 'This domain is not authorized for Google Sign-In. ';
                        errorMessage += 'Please try running the app from: ';
                        errorMessage += '1. Localhost (http://localhost) ';
                        errorMessage += '2. Firebase Hosting (devdiary-fff36.web.app) ';
                        errorMessage += '3. Or add your current domain to Firebase Console';
                        
                        // Show detailed instructions
                        console.log('=== GOOGLE SIGN-IN SETUP INSTRUCTIONS ===');
                        console.log('1. Go to Firebase Console: https://console.firebase.google.com/');
                        console.log('2. Select your project: devdiary-fff36');
                        console.log('3. Go to Authentication > Sign-in method');
                        console.log('4. Click on Google provider');
                        console.log('5. Under "Authorized domains", add your domain');
                        console.log('6. Common domains to add:');
                        console.log('   - localhost');
                        console.log('   - 127.0.0.1');
                        console.log('   - [your-custom-domain].com');
                        console.log('7. Save changes');
                    } else if (error.code === 'auth/network-request-failed') {
                        errorMessage += 'Network error. Please check your internet connection.';
                    } else if (error.code === 'auth/internal-error') {
                        errorMessage += 'Internal error. Please try again.';
                    } else {
                        errorMessage += error.message;
                    }
                    
                    showAlert(errorMessage, 'error');
                    
                    // Fallback: Try redirect method if popup fails
                    if (error.code === 'auth/popup-blocked' || error.code === 'auth/popup-closed-by-user') {
                        if (confirm('Popup authentication failed. Try redirect method?')) {
                            auth.signInWithRedirect(provider);
                        }
                    }
                });
        }

        // Check auth state for redirect result
        function checkRedirectResult() {
            auth.getRedirectResult()
                .then((result) => {
                    if (result.user) {
                        showAlert(`Welcome ${result.user.displayName}!`, 'success');
                    }
                })
                .catch((error) => {
                    console.error('Redirect result error:', error);
                });
        }

        // Handle register
        function handleRegister() {
            const name = document.getElementById('registerName').value;
            const email = document.getElementById('registerEmail').value;
            const password = document.getElementById('registerPassword').value;
            const confirmPassword = document.getElementById('registerConfirmPassword').value;
            
            if (!name || !email || !password || !confirmPassword) {
                showAlert('Please fill all fields', 'error');
                return;
            }
            
            if (password !== confirmPassword) {
                showAlert('Passwords do not match', 'error');
                return;
            }
            
            if (password.length < 6) {
                showAlert('Password must be at least 6 characters', 'error');
                return;
            }
            
            auth.createUserWithEmailAndPassword(email, password)
                .then((userCredential) => {
                    return userCredential.user.updateProfile({
                        displayName: name
                    });
                })
                .then(() => {
                    showAlert('Account created successfully!', 'success');
                    loadLoginPage();
                })
                .catch((error) => {
                    console.error('Registration error:', error);
                    let errorMessage = 'Registration failed. Please try again.';
                    
                    if (error.code === 'auth/email-already-in-use') {
                        errorMessage = 'Email already in use. Please use a different email.';
                    } else if (error.code === 'auth/invalid-email') {
                        errorMessage = 'Invalid email address.';
                    } else if (error.code === 'auth/weak-password') {
                        errorMessage = 'Password is too weak. Please use a stronger password.';
                    }
                    
                    showAlert(errorMessage, 'error');
                });
        }

        // Handle logout
        function handleLogout() {
            auth.signOut()
                .then(() => {
                    showAlert('Logged out successfully', 'success');
                    loadLoginPage();
                })
                .catch((error) => {
                    console.error('Logout error:', error);
                    showAlert('Logout failed. Please try again.', 'error');
                });
        }

        // Toggle menu
        function toggleMenu() {
            slideMenu.classList.toggle('open');
            menuOverlay.classList.toggle('visible');
        }

        // Close menu
        function closeSlideMenu() {
            slideMenu.classList.remove('open');
            menuOverlay.classList.remove('visible');
        }

        // Toggle add note section
        function toggleAddNoteSection() {
            addNoteSection.classList.toggle('visible');
            if (addNoteSection.classList.contains('visible')) {
                document.getElementById('note-title').focus();
            }
        }

        // Save note
        function saveNote() {
            const title = document.getElementById('note-title').value.trim();
            const description = document.getElementById('note-description').value.trim();
            
            if (!title) {
                showAlert('Please enter a title', 'error');
                return;
            }
            
            if (!currentUser) {
                showAlert('Please login to save notes', 'error');
                return;
            }
            
            const noteData = {
                title: title,
                description: description,
                tags: selectedTags,
                createdAt: firebase.firestore.FieldValue.serverTimestamp(),
                userId: currentUser.uid,
                userEmail: currentUser.email
            };
            
            db.collection('notes').add(noteData)
                .then(() => {
                    // Clear form
                    document.getElementById('note-title').value = '';
                    document.getElementById('note-description').value = '';
                    selectedTags = [];
                    
                    // Update tag options
                    tagOptions.forEach(tag => tag.classList.remove('selected'));
                    
                    // Hide form
                    addNoteSection.classList.remove('visible');
                    
                    showAlert('Note saved successfully!', 'success');
                })
                .catch((error) => {
                    console.error('Error saving note:', error);
                    showAlert('Failed to save note', 'error');
                });
        }

        // Load notes - FIXED VERSION (without composite index error)
        function loadNotes() {
            if (!currentUser) return;
            
            notesList.innerHTML = '<div class="loading"><div class="spinner"></div></div>';
            
            // Stop previous listener
            if (notesListener) {
                notesListener();
            }
            
            // FIX: First get all notes, then sort in JavaScript
            notesListener = db.collection('notes')
                .where('userId', '==', currentUser.uid)
                .onSnapshot((snapshot) => {
                    notes = [];
                    snapshot.forEach((doc) => {
                        const note = doc.data();
                        note.id = doc.id;
                        notes.push(note);
                    });
                    
                    // Sort notes by createdAt in JavaScript (newest first)
                    notes.sort((a, b) => {
                        const aTime = a.createdAt ? a.createdAt.toDate().getTime() : 0;
                        const bTime = b.createdAt ? b.createdAt.toDate().getTime() : 0;
                        return bTime - aTime;
                    });
                    
                    renderNotes();
                }, (error) => {
                    console.error('Error loading notes:', error);
                    
                    // If composite index error, try alternative approach
                    if (error.code === 'failed-precondition') {
                        // Try without ordering
                        loadNotesWithoutOrder();
                    } else {
                        notesList.innerHTML = `
                            <div class="empty-state">
                                <div class="empty-icon">
                                    <i class="fas fa-exclamation-circle"></i>
                                </div>
                                <h3 class="empty-title">Error loading notes</h3>
                                <p>Please try again</p>
                            </div>
                        `;
                    }
                });
        }

        // Alternative: Load notes without ordering
        function loadNotesWithoutOrder() {
            if (!currentUser) return;
            
            db.collection('notes')
                .where('userId', '==', currentUser.uid)
                .get()
                .then((snapshot) => {
                    notes = [];
                    snapshot.forEach((doc) => {
                        const note = doc.data();
                        note.id = doc.id;
                        notes.push(note);
                    });
                    
                    // Sort notes by createdAt in JavaScript
                    notes.sort((a, b) => {
                        const aTime = a.createdAt ? a.createdAt.toDate().getTime() : 0;
                        const bTime = b.createdAt ? b.createdAt.toDate().getTime() : 0;
                        return bTime - aTime;
                    });
                    
                    renderNotes();
                    
                    // Set up listener without ordering
                    notesListener = db.collection('notes')
                        .where('userId', '==', currentUser.uid)
                        .onSnapshot((snapshot) => {
                            notes = [];
                            snapshot.forEach((doc) => {
                                const note = doc.data();
                                note.id = doc.id;
                                notes.push(note);
                            });
                            
                            // Sort in JavaScript
                            notes.sort((a, b) => {
                                const aTime = a.createdAt ? a.createdAt.toDate().getTime() : 0;
                                const bTime = b.createdAt ? b.createdAt.toDate().getTime() : 0;
                                return bTime - aTime;
                            });
                            
                            renderNotes();
                        });
                })
                .catch((error) => {
                    console.error('Error loading notes without order:', error);
                    notesList.innerHTML = `
                        <div class="empty-state">
                            <div class="empty-icon">
                                <i class="fas fa-exclamation-circle"></i>
                            </div>
                            <h3 class="empty-title">Error loading notes</h3>
                            <p>${error.message}</p>
                        </div>
                    `;
                });
        }

        // Render notes
        function renderNotes() {
            if (notes.length === 0) {
                notesList.innerHTML = `
                    <div class="empty-state">
                        <div class="empty-icon">
                            <i class="fas fa-sticky-note"></i>
                        </div>
                        <h3 class="empty-title">No notes yet</h3>
                        <p>Create your first note to get started</p>
                    </div>
                `;
                return;
            }
            
            let filteredNotes = notes;
            
            // Apply category filter
            if (currentCategory !== 'all') {
                filteredNotes = filteredNotes.filter(note => 
                    note.tags && note.tags.includes(currentCategory)
                );
            }
            
            // Apply search filter
            if (searchQuery) {
                const query = searchQuery.toLowerCase();
                filteredNotes = filteredNotes.filter(note =>
                    note.title.toLowerCase().includes(query) ||
                    (note.description && note.description.toLowerCase().includes(query))
                );
            }
            
            if (filteredNotes.length === 0) {
                notesList.innerHTML = `
                    <div class="empty-state">
                        <div class="empty-icon">
                            <i class="fas fa-search"></i>
                        </div>
                        <h3 class="empty-title">No notes found</h3>
                        <p>Try a different search or filter</p>
                    </div>
                `;
                return;
            }
            
            notesList.innerHTML = filteredNotes.map(note => {
                const date = note.createdAt ? note.createdAt.toDate() : new Date();
                const formattedDate = date.toLocaleDateString('en-US', {
                    month: 'short',
                    day: 'numeric',
                    year: 'numeric'
                });
                
                const tagsHtml = note.tags && note.tags.length > 0 ? `
                    <div class="note-tags">
                        ${note.tags.map(tag => `<span class="note-tag">${tag}</span>`).join('')}
                    </div>
                ` : '';
                
                return `
                    <div class="note-card" data-id="${note.id}">
                        <div class="note-header">
                            <h3 class="note-title">${note.title}</h3>
                            <span class="note-date">${formattedDate}</span>
                        </div>
                        ${tagsHtml}
                        <div class="note-description">${note.description || ''}</div>
                        <div class="note-actions">
                            <button class="action-btn edit-note" data-id="${note.id}">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button class="action-btn delete-note" data-id="${note.id}">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </div>
                `;
            }).join('');
            
            // Add event listeners to note actions
            notesList.querySelectorAll('.edit-note').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const noteId = e.currentTarget.dataset.id;
                    editNote(noteId);
                });
            });
            
            notesList.querySelectorAll('.delete-note').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const noteId = e.currentTarget.dataset.id;
                    deleteNote(noteId);
                });
            });
        }

        // Edit note
        function editNote(noteId) {
            const note = notes.find(n => n.id === noteId);
            if (!note) return;
            
            const noteElement = document.querySelector(`.note-card[data-id="${noteId}"]`);
            
            noteElement.classList.add('edit-mode');
            noteElement.innerHTML = `
                <div class="note-header">
                    <input type="text" class="note-title-input" value="${note.title}" placeholder="Note title">
                    <span class="note-date">${new Date().toLocaleDateString('en-US', { month: 'short', day: 'numeric' })}</span>
                </div>
                
                <div class="form-group">
                    <label class="form-label">Tags</label>
                    <div class="tags-container">
                        <div class="tag-option ${(note.tags || []).includes('work') ? 'selected' : ''}" data-tag="work">Work</div>
                        <div class="tag-option ${(note.tags || []).includes('personal') ? 'selected' : ''}" data-tag="personal">Personal</div>
                        <div class="tag-option ${(note.tags || []).includes('ideas') ? 'selected' : ''}" data-tag="ideas">Ideas</div>
                        <div class="tag-option ${(note.tags || []).includes('important') ? 'selected' : ''}" data-tag="important">Important</div>
                    </div>
                </div>
                
                <textarea class="note-description-input" placeholder="Note description">${note.description || ''}</textarea>
                
                <div class="note-actions">
                    <button class="action-btn save-edit" data-id="${noteId}">
                        <i class="fas fa-save"></i>
                    </button>
                    <button class="action-btn cancel-edit" data-id="${noteId}">
                        <i class="fas fa-times"></i>
                    </button>
                </div>
            `;
            
            // Add event listeners for edit mode
            noteElement.querySelector('.save-edit').addEventListener('click', () => saveEdit(noteId));
            noteElement.querySelector('.cancel-edit').addEventListener('click', () => renderNotes());
            
            // Tag selection in edit mode
            noteElement.querySelectorAll('.tag-option').forEach(tag => {
                tag.addEventListener('click', function() {
                    this.classList.toggle('selected');
                });
            });
        }

        // Save edited note
        function saveEdit(noteId) {
            const noteElement = document.querySelector(`.note-card[data-id="${noteId}"]`);
            const title = noteElement.querySelector('.note-title-input').value.trim();
            const description = noteElement.querySelector('.note-description-input').value.trim();
            const selectedTags = Array.from(noteElement.querySelectorAll('.tag-option.selected'))
                .map(tag => tag.dataset.tag);
            
            if (!title) {
                showAlert('Please enter a title', 'error');
                return;
            }
            
            db.collection('notes').doc(noteId).update({
                title: title,
                description: description,
                tags: selectedTags
            })
            .then(() => {
                showAlert('Note updated successfully!', 'success');
            })
            .catch((error) => {
                console.error('Error updating note:', error);
                showAlert('Failed to update note', 'error');
            });
        }

        // Delete note
        function deleteNote(noteId) {
            if (!confirm('Are you sure you want to delete this note?')) {
                return;
            }
            
            db.collection('notes').doc(noteId).delete()
                .then(() => {
                    showAlert('Note deleted successfully', 'success');
                })
                .catch((error) => {
                    console.error('Error deleting note:', error);
                    showAlert('Failed to delete note', 'error');
                });
        }

        // Set theme
        function setTheme(theme) {
            document.body.className = `${theme}-theme`;
            
            // Update active theme option
            themeOptions.forEach(option => {
                if (option.dataset.theme === theme) {
                    option.classList.add('active');
                } else {
                    option.classList.remove('active');
                }
            });
            
            // Save theme preference
            localStorage.setItem('devdiary-theme', theme);
        }

        // Voice to text
        function setupVoiceToText() {
            if (!('webkitSpeechRecognition' in window) && !('SpeechRecognition' in window)) {
                micBtn.style.display = 'none';
                return;
            }
            
            const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            const recognition = new SpeechRecognition();
            
            recognition.continuous = true;
            recognition.interimResults = true;
            recognition.lang = 'en-US';
            
            micBtn.addEventListener('click', () => {
                if (isRecording) {
                    recognition.stop();
                } else {
                    recognition.start();
                }
            });
            
            recognition.onstart = () => {
                isRecording = true;
                micBtn.classList.add('recording');
                micBtn.innerHTML = '<i class="fas fa-stop"></i>';
                showAlert('Listening... Speak now', 'success');
            };
            
            recognition.onend = () => {
                isRecording = false;
                micBtn.classList.remove('recording');
                micBtn.innerHTML = '<i class="fas fa-microphone"></i>';
            };
            
            recognition.onresult = (event) => {
                let finalTranscript = '';
                
                for (let i = event.resultIndex; i < event.results.length; ++i) {
                    if (event.results[i].isFinal) {
                        finalTranscript += event.results[i][0].transcript;
                    }
                }
                
                if (finalTranscript) {
                    const textarea = document.getElementById('note-description');
                    const currentText = textarea.value;
                    const separator = (currentText && !currentText.endsWith(' ')) ? ' ' : '';
                    textarea.value = currentText + separator + finalTranscript;
                    textarea.scrollTop = textarea.scrollHeight;
                }
            };
            
            recognition.onerror = (event) => {
                console.error('Speech recognition error:', event.error);
                micBtn.classList.remove('recording');
                isRecording = false;
                if (event.error === 'not-allowed') {
                    showAlert('Microphone access denied', 'error');
                }
            };
        }

        // Get current domain
        function getCurrentDomain() {
            return window.location.hostname;
        }

        // Initialize app
        function initApp() {
            // Load saved theme
            const savedTheme = localStorage.getItem('devdiary-theme') || 'light';
            setTheme(savedTheme);
            
            // Check redirect result for Google auth
            checkRedirectResult();
            
            // Setup event listeners
            menuToggle.addEventListener('click', toggleMenu);
            closeMenu.addEventListener('click', closeSlideMenu);
            menuOverlay.addEventListener('click', closeSlideMenu);
            fabAddNote.addEventListener('click', toggleAddNoteSection);
            addNoteToggle.addEventListener('click', toggleAddNoteSection);
            cancelNoteBtn.addEventListener('click', toggleAddNoteSection);
            saveNoteBtn.addEventListener('click', saveNote);
            searchToggle.addEventListener('click', () => {
                searchBar.style.display = searchBar.style.display === 'none' ? 'block' : 'none';
                if (searchBar.style.display === 'block') {
                    searchInput.focus();
                }
            });
            
            searchInput.addEventListener('input', (e) => {
                searchQuery = e.target.value;
                renderNotes();
            });
            
            // Tag selection
            tagOptions.forEach(tag => {
                tag.addEventListener('click', function() {
                    const tagValue = this.dataset.tag;
                    
                    if (this.classList.contains('selected')) {
                        this.classList.remove('selected');
                        selectedTags = selectedTags.filter(t => t !== tagValue);
                    } else {
                        this.classList.add('selected');
                        selectedTags.push(tagValue);
                    }
                });
            });
            
            // Format buttons
            formatButtons.forEach(btn => {
                btn.addEventListener('click', function() {
                    const command = this.dataset.command;
                    document.execCommand(command, false, null);
                    document.getElementById('note-description').focus();
                });
            });
            
            // Menu category selection
            menuItems.forEach(item => {
                item.addEventListener('click', function() {
                    menuItems.forEach(i => i.classList.remove('active'));
                    this.classList.add('active');
                    currentCategory = this.dataset.category;
                    renderNotes();
                    closeSlideMenu();
                });
            });
            
            // Theme selection
            themeOptions.forEach(option => {
                option.addEventListener('click', function() {
                    const theme = this.dataset.theme;
                    setTheme(theme);
                });
            });
            
            // Logout
            menuLogout.addEventListener('click', handleLogout);
            
            // Setup voice to text
            setupVoiceToText();
            
            // Check current domain
            const currentDomain = getCurrentDomain();
            console.log('Current domain:', currentDomain);
            
            // If running locally, show helpful message
            if (currentDomain === 'localhost' || currentDomain === '127.0.0.1') {
                console.log('Running on localhost - Google Sign-In should work');
            } else {
                console.log('Running on domain:', currentDomain);
                console.log('Make sure this domain is authorized in Firebase Console');
            }
            
            // Auth state observer
            auth.onAuthStateChanged((user) => {
                currentUser = user;
                
                if (user) {
                    // Show main layout
                    mainLayout.style.display = 'block';
                    authContainer.style.display = 'none';
                    
                    // Update user info in menu
                    menuUserInfo.style.display = 'block';
                    menuUserName.textContent = user.displayName || 'User';
                    menuUserEmail.textContent = user.email;
                    
                    if (user.photoURL) {
                        menuUserAvatar.innerHTML = `<img src="${user.photoURL}" style="width:100%;height:100%;border-radius:50%;object-fit:cover;">`;
                    } else {
                        const initials = (user.displayName || 'U').split(' ').map(n => n[0]).join('').toUpperCase().substring(0, 2);
                        menuUserAvatar.textContent = initials;
                    }
                    
                    // Load notes
                    loadNotes();
                } else {
                    // Show auth pages
                    mainLayout.style.display = 'none';
                    authContainer.style.display = 'block';
                    loadLoginPage();
                    
                    // Clear notes listener
                    if (notesListener) {
                        notesListener();
                        notesListener = null;
                    }
                }
            });
        }

        // Initialize when DOM is loaded
        document.addEventListener('DOMContentLoaded', initApp);
    </script>
</body>
</html>
