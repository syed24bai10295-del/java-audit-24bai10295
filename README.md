# 💼 Personal Portfolio Tracker

> A command-line Java application to track your Stocks, Cryptocurrencies, and Mutual Funds — with persistent SQLite storage, file I/O export reports, and full OOP design.

---

##  Project Architecture

```
PortfolioTracker/
├── src/main/java/com/portfolio/
│   ├── Main.java                    ← Entry point (CLI menu)
│   ├── model/
│   │   ├── Asset.java               ← Abstract base class (OOP)
│   │   ├── Stock.java               ← Inherits Asset
│   │   ├── Crypto.java              ← Inherits Asset
│   │   ├── MutualFund.java          ← Inherits Asset
│   │   └── PortfolioSummary.java    ← Aggregated stats model
│   ├── dao/
│   │   └── AssetDAO.java            ← JDBC CRUD operations
│   ├── service/
│   │   └── PortfolioService.java    ← Business logic + Collections
│   ├── util/
│   │   ├── DatabaseManager.java     ← SQLite connection (Singleton)
│   │   ├── InputValidator.java      ← Input validation
│   │   └── ReportWriter.java        ← File I/O (CSV + TXT export)
│   └── exception/
│       ├── AssetNotFoundException.java
│       └── InvalidInputException.java
├── lib/                             ← JAR dependencies (auto-downloaded)
├── reports/                         ← Exported reports saved here
├── run.sh                           ← Linux/macOS build & run script
├── run.bat                          ← Windows build & run script
└── README.md
```

---

## ✨ Features

- **Add Assets** – Stocks, Cryptocurrencies, Mutual Funds
- **View Portfolio** – Formatted table with live P&L
- **Portfolio Summary** – Total invested, current value, overall return %
- **Update Prices** – Manually set current market prices
- **Delete Assets** – Remove assets with confirmation prompt
- **Search** – Find assets by symbol or name
- **Sort by P&L** – Best to worst performers
- **Group by Type** – Stocks vs Crypto vs Mutual Fund view
- **Export Reports** – CSV and formatted TXT reports
- **Persistent Storage** – SQLite database (`portfolio.db` auto-created)

---

## 🛠️ Setup & Run

### Prerequisites

| Requirement | Version | Download |
|-------------|---------|----------|
| Java JDK | 17 or higher | https://adoptium.net |

> **No Maven, no Gradle, no IDE required.** The scripts handle everything automatically — including downloading dependencies.

---

### Windows

```cmd
git clone https://github.com/Prabhatl0dhi/portfolio_tracker.git
cd portfolio_tracker
run.bat
```

> The script will automatically download `sqlite-jdbc.jar`, `slf4j-api.jar`, and `slf4j-simple.jar` into `lib\` if they are missing, then build and launch the app.

If Java is installed but not in PATH, the script will find it automatically in common install locations (`C:\Program Files\Java\`, `C:\Program Files\Eclipse Adoptium\`, etc.)

---

### Linux / macOS

```bash
git clone https://github.com/Prabhatl0dhi/portfolio_tracker.git
cd portfolio_tracker
chmod +x run.sh
./run.sh
```

> Same as Windows — dependencies are auto-downloaded via `curl` or `wget`.

---

### Manual Build (any OS)

If the scripts don't work, build manually:

**Step 1 – Download dependencies into `lib/`:**
- https://repo1.maven.org/maven2/org/xerial/sqlite-jdbc/3.45.1.0/sqlite-jdbc-3.45.1.0.jar → save as `lib/sqlite-jdbc.jar`
- https://repo1.maven.org/maven2/org/slf4j/slf4j-api/2.0.9/slf4j-api-2.0.9.jar → save as `lib/slf4j-api.jar`
- https://repo1.maven.org/maven2/org/slf4j/slf4j-simple/2.0.9/slf4j-simple-2.0.9.jar → save as `lib/slf4j-simple.jar`

**Step 2 – Compile:**
```bash
# Linux/macOS
mkdir -p out
javac -cp "lib/sqlite-jdbc.jar" -d out $(find src -name "*.java")

# Windows (Command Prompt)
mkdir out
dir /s /b src\main\java\*.java > sources.txt
javac -cp "lib\sqlite-jdbc.jar" -d out @sources.txt
del sources.txt
```

**Step 3 – Run:**
```bash
# Linux/macOS
java -cp "out:lib/sqlite-jdbc.jar:lib/slf4j-api.jar:lib/slf4j-simple.jar" com.portfolio.Main

# Windows
java -cp "out;lib\sqlite-jdbc.jar;lib\slf4j-api.jar;lib\slf4j-simple.jar" com.portfolio.Main
```

---

## 🚀 Usage

On launch you will see:

```
╔══════════════════════════════════════════════════════════╗
║          PERSONAL PORTFOLIO TRACKER  v1.0              ║
║        Track Stocks • Crypto • Mutual Funds              ║
╚══════════════════════════════════════════════════════════╝

═══════════════ MAIN MENU ═══════════════
  1. ➕  Add New Asset
  2. 📋  View All Assets
  3. 📊  Portfolio Summary
  4. 💲  Update Current Price
  5. 🗑️   Delete Asset
  6. 🔍  Search Assets
  7. 📈  View Sorted by P&L
  8. 🗂️   View by Asset Type
  9. 💾  Export Reports
  0. 🚪  Exit
═════════════════════════════════════════
```

### Sample Data to Test

| Type | Symbol | Name | Qty | Buy Price | Date |
|------|--------|------|-----|-----------|------|
| Stock | TCS | Tata Consultancy Services | 10 | 3800 | 2024-01-10 |
| Stock | INFY | Infosys | 20 | 1450 | 2024-02-01 |
| Crypto | BTC | Bitcoin | 0.05 | 3500000 | 2024-03-01 |
| Crypto | ETH | Ethereum | 1.5 | 230000 | 2024-02-15 |
| MF | HDFC-TOP100 | HDFC Top 100 Fund | 100 | 850 | 2024-01-20 |

After adding assets, use option **4** to update current prices and option **3** to see live P&L.

---

## 📖 Java Concepts Demonstrated

### OOP – Inheritance & Polymorphism
```java
// Abstract base class
public abstract class Asset {
    public abstract String getAssetType();
    public abstract double calculateCurrentValue(double price); // Polymorphism
}

// Subclass with method overriding
public class Stock extends Asset {
    @Override
    public double calculateCurrentValue(double price) {
        return quantity * price;
    }
}
```

### JDBC – Database Operations
```java
// PreparedStatement prevents SQL injection
PreparedStatement pstmt = connection.prepareStatement(
    "INSERT INTO assets (symbol, name, ...) VALUES (?, ?, ...)"
);
pstmt.setString(1, asset.getSymbol());
pstmt.executeUpdate();
```

### Collections Framework
```java
List<Asset> assets = new ArrayList<>();
Map<Integer, Double> prices = new HashMap<>();

// Sorting with Comparator
assets.sort((a, b) -> Double.compare(
    b.getProfitLoss(price), a.getProfitLoss(price)
));
```

### I/O Streams
```java
// Character-oriented stream (FileWriter + BufferedWriter)
BufferedWriter writer = new BufferedWriter(new FileWriter("report.csv"));

// Byte-oriented stream (FileOutputStream)
PrintWriter writer = new PrintWriter(
    new OutputStreamWriter(new FileOutputStream("report.txt"), StandardCharsets.UTF_8)
);
```

### Exception Handling
```java
try {
    portfolioService.deleteAsset(id);
} catch (AssetNotFoundException e) {
    System.err.println("Not found: " + e.getMessage());
} catch (InvalidInputException e) {
    System.err.println("Invalid input: " + e.getMessage());
}
```

---

## 📁 Database Schema

```sql
CREATE TABLE assets (
    id          INTEGER PRIMARY KEY AUTOINCREMENT,
    symbol      TEXT NOT NULL,
    name        TEXT NOT NULL,
    asset_type  TEXT NOT NULL,   -- 'STOCK', 'CRYPTO', 'MUTUAL_FUND'
    quantity    REAL NOT NULL,
    buy_price   REAL NOT NULL,
    buy_date    TEXT NOT NULL,
    currency    TEXT DEFAULT 'INR',
    extra1      TEXT,            -- exchange / blockchain / fund house
    extra2      TEXT,            -- sector / wallet / category
    extra3      TEXT             -- expense ratio (MF only)
);

CREATE TABLE price_history (
    id            INTEGER PRIMARY KEY AUTOINCREMENT,
    asset_id      INTEGER NOT NULL,
    price         REAL NOT NULL,
    recorded_date TEXT NOT NULL,
    FOREIGN KEY (asset_id) REFERENCES assets(id)
);
```

---

## 📝 References

1. Herbert Schildt, *Java The Complete Reference*, 11th Edition, Oracle Press, 2018
2. Deitel & Deitel, *Java How to Program*, 10th Edition, Pearson, 2015
3. SQLite JDBC Driver – https://github.com/xerial/sqlite-jdbc

---

