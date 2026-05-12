<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>FinTech Pro - Wealth Manager & Tracker</title>
    
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        :root {
            --primary-blue: #0f172a;
            --secondary-blue: #3b82f6;
            --success-green: #10b981;
            --danger-red: #ef4444;
            --gold-color: #f59e0b;
            --light-bg: #f8fafc;
        }

        body { font-family: 'Inter', sans-serif; background-color: var(--light-bg); color: #1e293b; }
        .navbar { background-color: var(--primary-blue); }
        .card { border: none; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.05); margin-bottom: 20px; }
        .card-header { background-color: white; border-bottom: 1px solid #e2e8f0; font-weight: 700; color: var(--primary-blue); padding: 12px 20px; border-radius: 12px 12px 0 0 !important; }
        
        .net-flow-card { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); color: white; }
        .grand-total-card { background: linear-gradient(135deg, #064e3b 0%, #10b981 100%); color: white; }
        
        .kpi-card { background: white; border-left: 4px solid; padding: 15px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.03); }
        .kpi-card.capital { border-color: var(--secondary-blue); }
        .kpi-card.manual { border-color: var(--gold-color); }

        .table thead th { background-color: #f1f5f9; color: var(--primary-blue); font-size: 0.75rem; text-transform: uppercase; }
        .asset-tag { padding: 4px 10px; border-radius: 6px; font-weight: 600; font-size: 0.85rem; }
        .tag-stock { background: #dbeafe; color: #1e40af; }
        .tag-gold { background: #fef3c7; color: #b45309; }
        .tag-usd { background: #d1fae5; color: #065f46; }
        .tag-safe { background: #e2e8f0; color: #334155; }

        .text-profit { color: var(--success-green) !important; font-weight: 600; }
        .text-loss { color: var(--danger-red) !important; font-weight: 600; }
        .scrollable-list { max-height: 150px; overflow-y: auto; padding-right: 5px; }
    </style>
</head>
<body>

<nav class="navbar navbar-dark mb-4 py-3">
    <div class="container-fluid px-4">
        <a class="navbar-brand fw-bold" href="#"><i class="fa-solid fa-wallet me-2"></i> FINTECH WEALTH PRO</a>
        <span class="navbar-text text-light d-none d-md-block">Hệ thống Phân bổ Tự động & Quản lý Tài sản Cộng dồn</span>
    </div>
</nav>

<div class="container-fluid px-4">
    <div class="row">
        <!-- CỘT TRÁI: Nhập liệu Thu/Chi và Lệnh Mua -->
        <div class="col-xl-3 col-lg-4">
            <!-- 1. Thu nhập / Chi phí (Auto Flow) -->
            <div class="card mb-3">
                <div class="card-header py-2"><i class="fa-solid fa-scale-balanced text-primary me-2"></i>CƠ SỞ DÒNG TIỀN</div>
                <div class="card-body p-3">
                    <div class="input-group input-group-sm mb-2">
                        <input type="text" id="incName" class="form-control" placeholder="Tên khoản thu">
                        <input type="number" id="incAmount" class="form-control" placeholder="Số tiền thu">
                        <button class="btn btn-success" onclick="addIncome()"><i class="fa-solid fa-plus"></i></button>
                    </div>
                    <div class="input-group input-group-sm mb-2">
                        <input type="text" id="expName" class="form-control" placeholder="Tên khoản chi">
                        <input type="number" id="expAmount" class="form-control" placeholder="Số tiền chi">
                        <button class="btn btn-danger" onclick="addExpense()"><i class="fa-solid fa-plus"></i></button>
                    </div>
                    <hr class="my-2">
                    <div class="d-flex justify-content-between small mb-1 text-muted"><span>Tổng Thu:</span><span id="totalIncome" class="text-success fw-bold">0 đ</span></div>
                    <div class="d-flex justify-content-between small text-muted"><span>Tổng Chi:</span><span id="totalExpense" class="text-danger fw-bold">0 đ</span></div>
                </div>
            </div>

            <div class="card net-flow-card p-3 mb-4 text-center">
                <div class="small fw-bold opacity-75">DÒNG TIỀN RÒNG ĐỂ TRÍCH LẬP TỰ ĐỘNG</div>
                <h3 id="netCashFlow" class="fw-bold my-2">0 đ</h3>
            </div>

            <!-- 2. Form Mua thêm hàng ngày -->
            <div class="card">
                <div class="card-header py-2 bg-light text-dark"><i class="fa-solid fa-cart-plus text-warning me-2"></i>NHẬP GIAO DỊCH MUA THÊM</div>
                <div class="card-body p-3">
                    <p class="small text-muted mb-2">Nhập các khoản bạn tự xuất tiền mua thêm hàng ngày (Cộng dồn vào tổng tài sản).</p>
                    <div class="mb-2">
                        <select id="tradeSymbol" class="form-select form-select-sm">
                            <option value="FPT">FPT - Công nghệ (CP)</option>
                            <option value="HPG">HPG - Thép (CP)</option>
                            <option value="VCB">VCB - Ngân hàng (CP)</option>
                            <option value="MWG">MWG - Bán lẻ (CP)</option>
                            <option value="GMD">GMD - Cảng biển (CP)</option>
                            <option value="SJC">VÀNG SJC (Chỉ)</option>
                            <option value="USD">USD/VND ($)</option>
                        </select>
                    </div>
                    <div class="row g-2 mb-3">
                        <div class="col-6">
                            <input type="number" id="tradeQty" class="form-control form-control-sm" placeholder="Khối lượng">
                        </div>
                        <div class="col-6">
                            <input type="number" id="tradePrice" class="form-control form-control-sm" placeholder="Giá mua (đ)">
                        </div>
                    </div>
                    <button class="btn btn-warning btn-sm w-100 fw-bold" onclick="addManualTrade()">XÁC NHẬN MUA</button>
                    
                    <hr class="my-3">
                    <div class="small fw-bold text-muted mb-2">LỊCH SỬ MUA THÊM:</div>
                    <div class="scrollable-list small text-muted" id="tradeHistoryList"></div>
                </div>
            </div>
        </div>

        <!-- CỘT PHẢI: Dashboard, Phân bổ & Tổng hợp -->
        <div class="col-xl-9 col-lg-8">
            
            <!-- HÀNG KPI TỔNG HỢP -->
            <div class="row mb-4 g-3">
                <div class="col-md-4">
                    <div class="kpi-card capital h-100">
                        <div class="small text-muted fw-bold">1. GIÁ TRỊ TỪ PHÂN BỔ TỰ ĐỘNG</div>
                        <h4 id="kpiAutoValue" class="fw-bold text-primary mt-1 mb-0">0 đ</h4>
                        <div class="small text-muted mt-1">Từ dòng tiền ròng hàng tháng</div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="kpi-card manual h-100">
                        <div class="small text-muted fw-bold">2. GIÁ TRỊ TỰ MUA THÊM HÀNG NGÀY</div>
                        <h4 id="kpiManualValue" class="fw-bold text-warning mt-1 mb-0" style="color: var(--gold-color) !important;">0 đ</h4>
                        <div class="small text-muted mt-1">Vốn bỏ ra: <span id="kpiManualCapital">0 đ</span></div>
                    </div>
                </div>
                <div class="col-md-4">
                    <div class="card grand-total-card p-3 h-100 mb-0 d-flex flex-column justify-content-center">
                        <div class="small fw-bold opacity-75">TỔNG TÀI SẢN THỰC TẾ (1 + 2)</div>
                        <h3 id="kpiGrandTotal" class="fw-bold my-1">0 đ</h3>
                        <div class="small fw-bold mt-1" id="kpiGrandProfit">Lãi/Lỗ tổng: 0 đ</div>
                    </div>
                </div>
            </div>

            <ul class="nav nav-tabs mb-3" id="myTab" role="tablist">
                <li class="nav-item" role="presentation">
                    <button class="nav-link active fw-bold" id="auto-tab" data-bs-toggle="tab" data-bs-target="#auto-pane" type="button" role="tab">Danh mục Phân bổ Tự động</button>
                </li>
                <li class="nav-item" role="presentation">
                    <button class="nav-link fw-bold" id="manual-tab" data-bs-toggle="tab" data-bs-target="#manual-pane" type="button" role="tab">Danh mục Tự mua thêm (Cộng dồn)</button>
                </li>
                <li class="nav-item" role="presentation">
                    <button class="nav-link fw-bold text-primary" id="cycle-tab" data-bs-toggle="tab" data-bs-target="#cycle-pane" type="button" role="tab">Chu kỳ 12 Tháng</button>
                </li>
            </ul>

            <div class="tab-content" id="myTabContent">
                <!-- TAB 1: DANH MỤC TỰ ĐỘNG (Giữ nguyên như cũ) -->
                <div class="tab-pane fade show active" id="auto-pane" role="tabpanel" tabindex="0">
                    <div class="card border-0 shadow-sm">
                        <div class="table-responsive">
                            <table class="table align-middle table-hover mb-0">
                                <thead>
                                    <tr>
                                        <th>Tài sản phân bổ</th>
                                        <th class="text-center">Tỷ trọng</th>
                                        <th class="text-end">Vốn Tự Động</th>
                                        <th class="text-end">Giá Mặc định</th>
                                        <th class="text-end">Giá Hiện Tại</th>
                                        <th class="text-end">Giá trị Hiện tại</th>
                                        <th class="text-end">Lãi/Lỗ (%)</th>
                                    </tr>
                                </thead>
                                <tbody id="autoPortfolioTable"></tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <!-- TAB 2: DANH MỤC MUA THÊM (MỚI) -->
                <div class="tab-pane fade" id="manual-pane" role="tabpanel" tabindex="0">
                    <div class="card border-0 shadow-sm">
                        <div class="table-responsive">
                            <table class="table align-middle table-hover mb-0">
                                <thead>
                                    <tr>
                                        <th>Mã Tài Sản</th>
                                        <th class="text-end">Tổng Khối lượng</th>
                                        <th class="text-end">Giá Vốn Trung Bình</th>
                                        <th class="text-end">Giá Hiện Tại (Thị trường)</th>
                                        <th class="text-end">Tổng Vốn Bỏ Ra</th>
                                        <th class="text-end">Tổng Giá Trị Hiện Tại</th>
                                        <th class="text-end">Lãi/Lỗ Thực Tế</th>
                                    </tr>
                                </thead>
                                <tbody id="manualPortfolioTable">
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>

                <!-- TAB 3: CHU KỲ 12 THÁNG (Giữ nguyên như cũ) -->
                <div class="tab-pane fade" id="cycle-pane" role="tabpanel" tabindex="0">
                    <div class="card border-0 shadow-sm">
                        <div class="table-responsive">
                            <table class="table table-bordered align-middle table-hover mb-0 text-center small">
                                <thead>
                                    <tr>
                                        <th>Tháng</th>
                                        <th>Vốn Bơm (Từ Dòng tiền ròng)</th>
                                        <th>Vốn Tích Lũy (Cộng dồn)</th>
                                        <th>Biến động thị trường</th>
                                        <th class="fw-bold" style="background-color: #f8fafc;">Tổng Tài Sản Mô Phỏng</th>
                                        <th>Tổng Lãi/Lỗ Tích Lũy</th>
                                    </tr>
                                </thead>
                                <tbody id="monthlyHistoryTable"></tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

        </div>
    </div>
</div>

<script>
    // Dữ liệu ban đầu
    let incomes = [{name: "Lương chính", amount: 35000000}, {name: "Làm thêm", amount: 5000000}];
    let expenses = [{name: "Sinh hoạt phí", amount: 12000000}, {name: "Trả góp", amount: 3000000}];
    
    // Lưu các lệnh người dùng tự nhập tay: { symbol, qty, price, date }
    let manualTrades = [];
    
    // Dữ liệu Danh mục Thị trường (Bao gồm Giá mua gốc cho phần tự động và Giá hiện tại chung)
    const marketData = [
        { type: 'stock', symbol: "FPT", name: "Công nghệ", alloc: 0.10, basePrice: 110000, currentPrice: 125000, unit: "CP" },
        { type: 'stock', symbol: "HPG", name: "Thép", alloc: 0.10, basePrice: 28000, currentPrice: 26500, unit: "CP" },
        { type: 'stock', symbol: "VCB", name: "Ngân hàng", alloc: 0.10, basePrice: 88000, currentPrice: 91000, unit: "CP" },
        { type: 'stock', symbol: "MWG", name: "Bán lẻ", alloc: 0.10, basePrice: 45000, currentPrice: 47500, unit: "CP" },
        { type: 'stock', symbol: "GMD", name: "Cảng biển", alloc: 0.10, basePrice: 70000, currentPrice: 74000, unit: "CP" },
        { type: 'gold', symbol: "SJC", name: "Vàng miếng", alloc: 0.10, basePrice: 8200000, currentPrice: 8750000, unit: "Chỉ" },
        { type: 'usd', symbol: "USD", name: "Ngoại tệ", alloc: 0.10, basePrice: 24500, currentPrice: 25400, unit: "$" },
        { type: 'safe', symbol: "TIẾT KIỆM", name: "Lãi suất 5%/năm", alloc: 0.10, basePrice: 1, currentPrice: 1.0041, unit: "đ" }, 
        { type: 'safe', symbol: "TIỀN MẶT", name: "Thanh khoản", alloc: 0.20, basePrice: 1, currentPrice: 1, unit: "đ" } 
    ];

    const monthlyMarketFluctuations = [1.5, -0.8, 2.1, 1.0, -1.5, 3.2, 0.5, 1.8, -1.2, 2.5, 1.5, 2.8]; 

    function formatVND(num) { return new Intl.NumberFormat('vi-VN').format(Math.round(num)) + ' đ'; }

    // Xử lý Thu Chi (Dòng tiền ròng tự động)
    function addIncome() {
        const name = document.getElementById('incName').value; const amount = parseFloat(document.getElementById('incAmount').value);
        if(name && amount) { incomes.push({name, amount}); document.getElementById('incName').value = ''; document.getElementById('incAmount').value = ''; renderAll(); }
    }
    function addExpense() {
        const name = document.getElementById('expName').value; const amount = parseFloat(document.getElementById('expAmount').value);
        if(name && amount) { expenses.push({name, amount}); document.getElementById('expName').value = ''; document.getElementById('expAmount').value = ''; renderAll(); }
    }

    // Xử lý Giao dịch Mua tay cộng dồn
    function addManualTrade() {
        const symbol = document.getElementById('tradeSymbol').value;
        const qty = parseFloat(document.getElementById('tradeQty').value);
        const price = parseFloat(document.getElementById('tradePrice').value);
        
        if (!qty || !price || qty <= 0 || price <= 0) { alert("Vui lòng nhập khối lượng và giá hợp lệ!"); return; }
        
        const date = new Date().toLocaleDateString('vi-VN', { day: '2-digit', month: '2-digit' });
        manualTrades.push({ symbol, qty, price, date });
        
        document.getElementById('tradeQty').value = ''; document.getElementById('tradePrice').value = '';
        renderAll();
    }

    function renderAll() {
        // 1. Tính Dòng tiền ròng
        let totalInc = incomes.reduce((s, i) => s + i.amount, 0);
        let totalExp = expenses.reduce((s, i) => s + i.amount, 0);
        document.getElementById('totalIncome').innerText = formatVND(totalInc);
        document.getElementById('totalExpense').innerText = formatVND(totalExp);
        
        const netFlow = totalInc - totalExp;
        const validFlow = netFlow > 0 ? netFlow : 0;
        document.getElementById('netCashFlow').innerText = formatVND(validFlow);

        // 2. Render Tab 1: Danh mục Tự động
        let autoTableHtml = '';
        let totalAutoCapital = 0;
        let totalAutoCurrentValue = 0;

        marketData.forEach(asset => {
            let autoMoney = validFlow * asset.alloc;
            let currentVal = autoMoney * (asset.currentPrice / asset.basePrice);
            let profitAmt = currentVal - autoMoney;
            let profitPct = autoMoney > 0 ? (profitAmt / autoMoney) * 100 : 0;
            
            totalAutoCapital += autoMoney;
            totalAutoCurrentValue += currentVal;

            let pClass = profitAmt >= 0 ? 'text-profit' : 'text-loss';
            let sign = profitAmt >= 0 ? '+' : '';
            let tagClass = asset.type === 'stock' ? 'tag-stock' : (asset.type === 'gold' ? 'tag-gold' : (asset.type === 'usd' ? 'tag-usd' : 'tag-safe'));

            let displayBase = asset.type === 'safe' ? '-' : formatVND(asset.basePrice);
            let displayCur = asset.type === 'safe' ? '-' : formatVND(asset.currentPrice);

            autoTableHtml += `
                <tr>
                    <td><div class="asset-tag ${tagClass} d-inline-block">${asset.symbol}</div> <span class="small text-muted ms-1">${asset.name}</span></td>
                    <td class="text-center fw-bold">${(asset.alloc * 100).toFixed(0)}%</td>
                    <td class="text-end">${formatVND(autoMoney)}</td>
                    <td class="text-end text-muted small">${displayBase}</td>
                    <td class="text-end fw-bold small">${displayCur}</td>
                    <td class="text-end fw-bold text-primary">${formatVND(currentVal)}</td>
                    <td class="text-end ${pClass}"><small>${sign}${formatVND(profitAmt)} (${sign}${profitPct.toFixed(1)}%)</small></td>
                </tr>
            `;
        });
        document.getElementById('autoPortfolioTable').innerHTML = autoTableHtml;
        document.getElementById('kpiAutoValue').innerText = formatVND(totalAutoCurrentValue);

        // 3. Render Lịch sử Sổ lệnh (Cột trái)
        let txHtml = '';
        [...manualTrades].reverse().forEach(t => {
            txHtml += `<div class="d-flex justify-content-between border-bottom pb-1 mb-1">
                <span><b class="text-primary">${t.symbol}</b> <span style="font-size: 0.7rem">(${t.date})</span></span>
                <span>+${t.qty} <small>@${formatVND(t.price).replace(' đ','')}</small></span>
            </div>`;
        });
        document.getElementById('tradeHistoryList').innerHTML = txHtml || 'Chưa có mua thêm.';

        // 4. Render Tab 2: Danh mục Mua tay (Gom nhóm)
        let manualPortfolio = {};
        let totalManualCapital = 0;
        let totalManualCurrentValue = 0;

        manualTrades.forEach(t => {
            if (!manualPortfolio[t.symbol]) manualPortfolio[t.symbol] = { qty: 0, cost: 0 };
            manualPortfolio[t.symbol].qty += t.qty;
            manualPortfolio[t.symbol].cost += (t.qty * t.price);
        });

        let manualTableHtml = '';
        for (let sym in manualPortfolio) {
            let data = manualPortfolio[sym];
            let assetRef = marketData.find(a => a.symbol === sym);
            
            let avgPrice = data.cost / data.qty;
            let currentVal = data.qty * assetRef.currentPrice;
            let profitAmt = currentVal - data.cost;
            let profitPct = (profitAmt / data.cost) * 100;

            totalManualCapital += data.cost;
            totalManualCurrentValue += currentVal;

            let pClass = profitAmt >= 0 ? 'text-profit' : 'text-loss';
            let sign = profitAmt >= 0 ? '+' : '';
            let tagClass = assetRef.type === 'stock' ? 'tag-stock' : (assetRef.type === 'gold' ? 'tag-gold' : 'tag-usd');

            manualTableHtml += `
                <tr>
                    <td><div class="asset-tag ${tagClass}">${sym}</div></td>
                    <td class="text-end fw-bold">${data.qty} <small class="fw-normal">${assetRef.unit}</small></td>
                    <td class="text-end">${formatVND(avgPrice)}</td>
                    <td class="text-end fw-bold">${formatVND(assetRef.currentPrice)}</td>
                    <td class="text-end">${formatVND(data.cost)}</td>
                    <td class="text-end fw-bold text-warning" style="color: var(--gold-color) !important;">${formatVND(currentVal)}</td>
                    <td class="text-end ${pClass}">${sign}${formatVND(profitAmt)} <br><small>(${sign}${profitPct.toFixed(2)}%)</small></td>
                </tr>
            `;
        }
        if(!manualTableHtml) manualTableHtml = '<tr><td colspan="7" class="text-center text-muted py-4">Bạn chưa nhập khoản mua thêm nào.</td></tr>';
        
        document.getElementById('manualPortfolioTable').innerHTML = manualTableHtml;
        document.getElementById('kpiManualCapital').innerText = formatVND(totalManualCapital);
        document.getElementById('kpiManualValue').innerText = formatVND(totalManualCurrentValue);

        // 5. Tính TỔNG TÀI SẢN THỰC TẾ (Auto + Manual)
        let grandTotalValue = totalAutoCurrentValue + totalManualCurrentValue;
        let grandTotalCapital = totalAutoCapital + totalManualCapital;
        let grandProfit = grandTotalValue - grandTotalCapital;
        let grandSign = grandProfit >= 0 ? '+' : '';
        
        document.getElementById('kpiGrandTotal').innerText = formatVND(grandTotalValue);
        document.getElementById('kpiGrandProfit').innerText = `Lãi/Lỗ tổng: ${grandSign}${formatVND(grandProfit)}`;

        // 6. Render Tab 3: Chu kỳ 12 Tháng (Chỉ tính dựa trên Dòng tiền ròng mô phỏng như v4)
        renderMonthlyHistory(validFlow);
    }

    function renderMonthlyHistory(monthlyInvestment) {
        let historyHtml = ''; let accCapital = 0; let accValue = 0;
        if(monthlyInvestment === 0) {
            document.getElementById('monthlyHistoryTable').innerHTML = '<tr><td colspan="6" class="text-muted">Chưa có dòng tiền tự động.</td></tr>'; return;
        }
        for (let i = 0; i < 12; i++) {
            accCapital += monthlyInvestment;
            let mPct = monthlyMarketFluctuations[i];
            accValue = (accValue * (1 + (mPct / 100))) + monthlyInvestment; 
            let totalPnL = accValue - accCapital;
            
            historyHtml += `<tr>
                <td class="fw-bold text-primary">Tháng ${i + 1}</td>
                <td>+${formatVND(monthlyInvestment)}</td>
                <td class="text-muted">${formatVND(accCapital)}</td>
                <td class="${mPct > 0 ? 'text-profit' : 'text-loss'} fw-bold">${mPct > 0 ? '+' : ''}${mPct}%</td>
                <td class="fw-bold" style="background-color: #f8fafc;">${formatVND(accValue)}</td>
                <td class="${totalPnL > 0 ? 'text-profit' : 'text-loss'}">${totalPnL > 0 ? '+' : ''}${formatVND(totalPnL)}</td>
            </tr>`;
        }
        document.getElementById('monthlyHistoryTable').innerHTML = historyHtml;
    }

    renderAll();
</script>

<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
