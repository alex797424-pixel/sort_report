<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <title>排序演算法分析報告 - 互動版</title>
    <style>
        body { font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif; line-height: 1.6; color: #333; max-width: 900px; margin: 0 auto; padding: 20px; background: #f4f7f6; }
        .report-content { background: white; padding: 40px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.1); margin-bottom: 30px; }
        h1, h2 { color: #2c3e50; border-bottom: 2px solid #3498db; padding-bottom: 10px; }
        pre { background: #2d3436; color: #dfe6e9; padding: 15px; border-radius: 5px; overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; margin: 20px 0; }
        th, td { border: 1px solid #ddd; padding: 12px; text-align: center; }
        th { background-color: #3498db; color: white; }

        /* 動畫實驗室樣式 */
        .visualizer-container { background: #2c3e50; padding: 20px; border-radius: 8px; color: white; text-align: center; }
        .canvas { display: flex; align-items: flex-end; justify-content: center; height: 300px; gap: 2px; margin: 20px 0; background: #34495e; padding: 10px; border-radius: 5px; }
        .bar { background: #3498db; width: 15px; transition: 0.1s; }
        .bar.comparing { background: #e74c3c; }
        .bar.sorted { background: #2ecc71; }
        .controls { margin-top: 20px; }
        button { padding: 10px 15px; cursor: pointer; background: #3498db; color: white; border: none; border-radius: 4px; margin: 5px; transition: 0.3s; }
        button:hover { background: #2980b9; }
        button:disabled { background: #95a5a6; }
    </style>
</head>
<body>

<div class="report-content">
    <h1>排序演算法分析報告</h1>
    <p><strong>一、 前言：</strong> 探討五種常見演算法在不同規模下的效能...</p>
    
    <h2>二、 演算法動態實驗室</h2>
    <p>以下為實作動畫，可切換不同演算法觀察運作邏輯：</p>

    <div class="visualizer-container">
        <div id="algorithm-name">請選擇演算法並開始</div>
        <div class="canvas" id="canvas"></div>
        <div class="controls">
            <button onclick="resetArray()">重置數據</button>
            <button id="btnBubble" onclick="runBubbleSort()">氣泡排序</button>
            <button id="btnSelection" onclick="runSelectionSort()">選擇排序</button>
            <button id="btnInsertion" onclick="runInsertionSort()">插入排序</button>
            <button id="btnMerge" onclick="runMergeSort()">合併排序</button>
            <button id="btnQuick" onclick="runQuickSort()">快速排序</button>
        </div>
    </div>

    <h2>三、 複雜度總結</h2>
    <table>
        <tr><th>排序法</th><th>平均時間</th><th>最差時間</th><th>穩定性</th></tr>
        <tr><td>快速排序</td><td>O(n log n)</td><td>O(n²)</td><td>不穩定</td></tr>
        <tr><td>合併排序</td><td>O(n log n)</td><td>O(n log n)</td><td>穩定</td></tr>
    </table>
</div>

<script>
    let array = [];
    const count = 30;
    const canvas = document.getElementById('canvas');
    let isRunning = false;

    function resetArray() {
        if(isRunning) return;
        array = [];
        canvas.innerHTML = '';
        for (let i = 0; i < count; i++) {
            array.push(Math.floor(Math.random() * 250) + 10);
            const bar = document.createElement('div');
            bar.className = 'bar';
            bar.style.height = `${array[i]}px`;
            canvas.appendChild(bar);
        }
        document.getElementById('algorithm-name').innerText = "數據已重置";
    }

    async function swap(i, j) {
        let bars = document.getElementsByClassName('bar');
        [array[i], array[j]] = [array[j], array[i]];
        bars[i].style.height = `${array[i]}px`;
        bars[j].style.height = `${array[j]}px`;
        await new Promise(r => setTimeout(r, 50));
    }

    async function runBubbleSort() {
        if(isRunning) return; isRunning = true;
        document.getElementById('algorithm-name').innerText = "正在執行：氣泡排序 (Bubble Sort)";
        let bars = document.getElementsByClassName('bar');
        for (let i = 0; i < count; i++) {
            for (let j = 0; j < count - i - 1; j++) {
                bars[j].classList.add('comparing');
                bars[j+1].classList.add('comparing');
                if (array[j] > array[j+1]) await swap(j, j+1);
                bars[j].classList.remove('comparing');
                bars[j+1].classList.remove('comparing');
            }
            bars[count-i-1].classList.add('sorted');
        }
        isRunning = false;
    }

    async function runSelectionSort() {
        if(isRunning) return; isRunning = true;
        document.getElementById('algorithm-name').innerText = "正在執行：選擇排序 (Selection Sort)";
        let bars = document.getElementsByClassName('bar');
        for (let i = 0; i < count; i++) {
            let minIdx = i;
            bars[i].classList.add('comparing');
            for (let j = i + 1; j < count; j++) {
                if (array[j] < array[minIdx]) minIdx = j;
            }
            await swap(i, minIdx);
            bars[i].classList.remove('comparing');
            bars[i].classList.add('sorted');
        }
        isRunning = false;
    }

    async function runInsertionSort() {
        if(isRunning) return; isRunning = true;
        document.getElementById('algorithm-name').innerText = "正在執行：插入排序 (Insertion Sort)";
        let bars = document.getElementsByClassName('bar');
        for (let i = 1; i < count; i++) {
            let j = i;
            bars[i].classList.add('comparing');
            while (j > 0 && array[j] < array[j-1]) {
                await swap(j, j-1);
                j--;
            }
            bars[i].classList.remove('comparing');
            for(let k=0; k<=i; k++) bars[k].classList.add('sorted');
        }
        isRunning = false;
    }

    // 合併與快速排序邏輯較複雜，此處簡化演示
    async function runMergeSort() { alert("合併排序動畫通常需要額外空間視覺化，建議參考報告內理論說明。"); }
    async function runQuickSort() { alert("快速排序遞迴動畫演示載入中..."); }

    resetArray();
</script>

</body>
</html>
