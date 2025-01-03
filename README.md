<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Comprehensive Calculator</title>
    <style>
        body { font-family: Arial, sans-serif; display: flex; justify-content: center; align-items: center; height: 100vh; background-color: #f5f5f5; margin: 0; }
        .calculator-container { background-color: #ffffff; padding: 20px; border-radius: 10px; box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1); }
        .calculator-display { width: 100%; padding: 10px; font-size: 18px; margin-bottom: 20px; border: 1px solid #ddd; border-radius: 5px; text-align: right; height: 80px; }
        .calculator-buttons { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
        .calculator-button { padding: 20px; font-size: 18px; border: none; border-radius: 5px; background-color: #007bff; color: #ffffff; cursor: pointer; text-align: center; }
        .calculator-button:hover { background-color: #0056b3; }
        .instructions { margin-bottom: 20px; }
        .fraction-controls { display: flex; justify-content: space-between; margin-top: 10px; }
        .memory-container { margin-top: 20px; display: none; }
        .memory-item { background-color: #f5f5f5; padding: 10px; border: 1px solid #ddd; border-radius: 5px; margin-bottom: 5px; }
        .view-memory-button { padding: 10px; font-size: 18px; border: none; border-radius: 5px; background-color: #28a745; color: #ffffff; cursor: pointer; text-align: center; margin-top: 20px; }
        .view-memory-button:hover { background-color: #218838; }
        .hide-memory-button { padding: 10px; font-size: 18px; border: none; border-radius: 5px; background-color: #dc3545; color: #ffffff; cursor: pointer; text-align: center; margin-top: 20px; display: none; }
        .hide-memory-button:hover { background-color: #c82333; }
    </style>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjs/10.0.2/math.min.js"></script>
</head>
<body>
    <div class="calculator-container">
        <div class="instructions">
            <p>1. لحساب الجذر:</p>
            <ul>
                <li>أدخل درجة الجذر في الحقل النصي.</li>
                <li>اضغط على زر الجذر (nth√).</li>
                <li>أدخل الرقم الذي تريد حسابه.</li>
                <li>اضغط على زر = لرؤية الناتج.</li>
            </ul>
            <p>2. لحساب الأسس:</p>
            <ul>
                <li>أدخل القاعدة.</li>
                <li>اضغط على زر الأس (أس).</li>
                <li>أدخل قيمة الأس.</li>
                <li>اضغط على زر = لرؤية الناتج.</li>
            </ul>
        </div>
        <input type="text" id="degree" class="calculator-display" placeholder="أدخل درجة الجذر">
        <input type="text" id="result" class="calculator-display" disabled>
        <div class="calculator-buttons">
            <button class="calculator-button" onclick="clearResult()">C</button>
            <button class="calculator-button" onclick="deleteLast()">DEL</button>
            <button class="calculator-button" onclick="prepareRoot()">nth√</button>
            <button class="calculator-button" onclick="appendOperator('÷')">÷</button>
            <button class="calculator-button" onclick="appendNumber(7)">7</button>
            <button class="calculator-button" onclick="appendNumber(8)">8</button>
            <button class="calculator-button" onclick="appendNumber(9)">9</button>
            <button class="calculator-button" onclick="appendOperator('×')">×</button>
            <button class="calculator-button" onclick="appendNumber(4)">4</button>
            <button class="calculator-button" onclick="appendNumber(5)">5</button>
            <button class="calculator-button" onclick="appendNumber(6)">6</button>
            <button class="calculator-button" onclick="handleMinus()">-</button>
            <button class="calculator-button" onclick="appendNumber(1)">1</button>
            <button class="calculator-button" onclick="appendNumber(2)">2</button>
            <button class="calculator-button" onclick="appendNumber(3)">3</button>
            <button class="calculator-button" onclick="appendOperator('+')">+</button>
            <button class="calculator-button" onclick="appendDot()">.</button>
            <button class="calculator-button" onclick="appendNumber(0)">0</button>
            <button class="calculator-button" onclick="appendPowerOperator()">أس</button>
            <button class="calculator-button" onclick="calculateResult()">=</button>
            <button class="calculator-button" onclick="appendBracket('(')">(</button>
            <button class="calculator-button" onclick="appendBracket(')')">)</button>
        </div>
        <div class="fraction-controls" id="fraction-controls" style="display: none;">
            <button class="calculator-button" onclick="moveUp()">↑</button>
            <button class="calculator-button" onclick="moveDown()">↓</button>
        </div>
        <div class="memory-container" id="memory-container"></div>
        <button class="view-memory-button" id="view-memory-button" onclick="viewMemory()">عرض العمليات السابقة</button>
        <button class="hide-memory-button" id="hide-memory-button" onclick="hideMemory()">إخفاء العمليات السابقة</button>
    </div>
    <script>
        let rootDegree = null;
        let isRootOperation = false;
        let isPowerOperation = false;
        let isFractionOperation = false;
        let fractionPart = "numerator";
        let isNegativeEnabled = false;
        let memory = [];

        function appendNumber(number) {
            let resultField = document.getElementById("result");
            if (isFractionOperation) {
                if (fractionPart === "numerator") {
                    resultField.value = resultField.value.replace("□", number);
                } else {
                    resultField.value = resultField.value.replace("□", number);
                }
            } else {
                resultField.value += number;
            }
        }

        function appendOperator(operator) {
            document.getElementById("result").value += " " + operator + " ";
        }

        function appendDot() {
            document.getElementById("result").value += ".";
        }

        function appendBracket(bracket) {
            document.getElementById("result").value += bracket;
        }

        function clearResult() {
            document.getElementById("result").value = "";
            document.getElementById("degree").value = "";
            rootDegree = null;
            isRootOperation = false;
            isPowerOperation = false;
            isFractionOperation = false;
            fractionPart = "numerator";
            document.getElementById("fraction-controls").style.display = "none";
        }

        function deleteLast() {
            let resultField = document.getElementById("result");
            resultField.value = resultField.value.slice(0, -1);
        }

        function calculateResult() {
            let resultField = document.getElementById("result");
            try {
                let expression = resultField.value;

                // حل ما داخل الأقواس أولاً
                while (expression.includes("(")) {
                    expression = expression.replace(/\(([^\(\)]+)\)/g, function (match, group) {
                        return math.evaluate(group.replace(/×/g, '*').replace(/÷/g, '/'));
                    });
                }

                // حل الأسس السالبة والأعداد الزوجية
                expression = expression.replace(/(-?\d+)\^(\d+)/g, function (match, base, exponent) {
                    base = parseFloat(base);
                    exponent = parseInt(exponent);
                    if (base < 0 && exponent % 2 === 0) {
                        return Math.pow(Math.abs(base), exponent);
                    } else {
                        return Math.pow(base, exponent);
                    }
                });

                // حل الجذور
                expression = expression.replace(/(\d+)√(\d+)/g, function (match, degree, number) {
                    return math.pow(parseFloat(number), 1 / parseFloat(degree));
                });

                // حل الضرب والقسمة
                expression = expression.replace(/(\د+)\س*×\س*(\د+)/g, function (match, a, b) {
                    return parseFloat(a) * parseFloat(b);
                });
                expression = expression.replace(/(\د+)\س*÷\س*(\د+)/g, function (match, a, b) {
                    return parseFloat(a) / parseFloat(b);
                });

                // حل الجمع والطرح بتطبيق قواعد الإشارات
                expression = expression.replace(/(-?\د+)\س*\+\س*(-?\د+)/g, function (match, a, b) {
                    a = parseFloat(a);
                    b = parseFloat(b);
                    if (a < 0 && b < 0) {
                        return a + b;
                    } else if (a > 0 && b > 0) {
                        return a + b;
                    } else {
                        return a + b; // استخدام الطرح هنا يؤدي إلى الجمع لأن الإشارة مختلفة
                    }
                });

                expression = expression.replace(/(-?\د+)\س*-\س*(-?\د+)/g, function (match, a, b) {
                    a = parseFloat(a);
                    b = parseFloat(b);
                    if (a < 0 && b < 0) {
                        return a - b;
                    } else if (a > 0 && b > 0) {
                        return a - b;
                    } else {
                        return a - b;
                    }
                });

                resultField.value = math.evaluate(expression.replace(/×/g, '*').replace(/÷/g, '/')).toFixed(10);

                saveToMemory(expression, resultField.value);
            } catch (e) {
                alert("خطأ في الحساب: " + e.message);
                clearResult();
            }
        }

        function prepareRoot() {
            rootDegree = parseFloat(document.getElementById("degree").value);
            if (!rootDegree) {
                alert("يرجى إدخال درجة الجذر أولاً");
            } else {
                document.getElementById("result").value += `${rootDegree}√`;
                isRootOperation = true;
            }
        }
        function appendPowerOperator() {
            let resultField = document.getElementById("result");
            resultField.value += "^";
            isPowerOperation = true;
        }

        function appendFraction() {
            let resultField = document.getElementById("result");
            resultField.value += "□/□";
            isFractionOperation = true;
            document.getElementById("fraction-controls").style.display = "flex";
        }

        function moveUp() {
            fractionPart = "numerator";
        }

        function moveDown() {
            fractionPart = "denominator";
        }

        function handleMinus() {
            let resultField = document.getElementById("result");
            if (resultField.value === "" || resultField.value.slice(-1) === " ") {
                resultField.value += "-";
            } else {
                resultField.value += " - ";
            }
        }

        function saveToMemory(expression, result) {
            memory.push({expression, result});
            updateMemoryDisplay();
        }

        function viewMemory() {
            updateMemoryDisplay();
            document.getElementById("memory-container").style.display = "block";
            document.getElementById("view-memory-button").style.display = "none";
            document.getElementById("hide-memory-button").style.display = "block";
        }

        function hideMemory() {
            document.getElementById("memory-container").style.display = "none";
            document.getElementById("view-memory-button").style.display = "block";
            document.getElementById("hide-memory-button").style.display = "none";
        }

        function updateMemoryDisplay() {
            const memoryContainer = document.getElementById("memory-container");
            memoryContainer.innerHTML = "";
            memory.forEach((item, index) => {
                const memoryItem = document.createElement("div");
                memoryItem.className = "memory-item";
                memoryItem.textContent = `عملية ${index + 1}: ${item.expression} = ${item.result}`;
                memoryContainer.appendChild(memoryItem);
            });
        }
    </script>
</body>
</html>
