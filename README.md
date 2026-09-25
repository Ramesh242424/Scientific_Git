# Scientific_Git
## Aim
  To develop a Scientific Calculator Android Application that performs basic arithmetic and scientific mathematical operations such as addition, subtraction, multiplication, division, sine, cosine, tangent, square root, logarithm, natural logarithm, square, power, percentage, brackets, π, and e.

## Algorithm
Start the Android application.


Display the scientific calculator interface with a display screen and function buttons.

Initialize all number, operator, and scientific function buttons.

When a number is pressed, append it to the display.

When an arithmetic operator is pressed, append the corresponding operator to the expression.

For scientific functions such as sin, cos, tan, √, log, and ln, append the required function to the expression.

Allow constants such as π and e, along with brackets and percentage.

When the = button is pressed, parse the complete mathematical expression.

Perform the required operations according to mathematical precedence.

Display the calculated result on the screen.

If an invalid expression or mathematical error occurs, display Error.

Stop the operation when the application is closed.

## Program 

## MainActivity.java
```
package com.example.scientificcalculator;

import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

import java.util.Locale;

public class MainActivity extends AppCompatActivity {

    private EditText display;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        display = findViewById(R.id.display);

        setupNumberButtons();
        setupOperatorButtons();
        setupScientificButtons();
    }

    // ----------------------------------------------------
    // NUMBER BUTTONS
    // ----------------------------------------------------

    private void setupNumberButtons() {

        int[] numberIds = {
                R.id.btn0,
                R.id.btn1,
                R.id.btn2,
                R.id.btn3,
                R.id.btn4,
                R.id.btn5,
                R.id.btn6,
                R.id.btn7,
                R.id.btn8,
                R.id.btn9
        };

        for (int id : numberIds) {

            Button button = findViewById(id);

            button.setOnClickListener(v -> {
                Button b = (Button) v;
                appendText(b.getText().toString());
            });
        }

        findViewById(R.id.btnDot).setOnClickListener(v -> {

            String text = display.getText().toString();

            if (text.isEmpty()) {
                appendText("0.");
                return;
            }

            // Prevent multiple decimal points in the same number
            String[] parts = text.split("[+\\-×÷^()*/]");

            String lastPart = parts.length > 0
                    ? parts[parts.length - 1]
                    : "";

            if (!lastPart.contains(".")) {
                appendText(".");
            }
        });
    }

    // ----------------------------------------------------
    // BASIC OPERATORS
    // ----------------------------------------------------

    private void setupOperatorButtons() {

        findViewById(R.id.btnPlus).setOnClickListener(v ->
                appendOperator("+"));

        findViewById(R.id.btnMinus).setOnClickListener(v ->
                appendOperator("-"));

        findViewById(R.id.btnMultiply).setOnClickListener(v ->
                appendOperator("*"));

        findViewById(R.id.btnDivide).setOnClickListener(v ->
                appendOperator("/"));

        findViewById(R.id.btnPower).setOnClickListener(v ->
                appendOperator("^"));

        // Percentage
        findViewById(R.id.btnPercent).setOnClickListener(v -> {

            String text = display.getText().toString();

            if (!text.isEmpty()) {
                appendText("%");
            }
        });

        // Open bracket
        findViewById(R.id.btnOpen).setOnClickListener(v ->
                appendText("("));

        // Close bracket
        findViewById(R.id.btnClose).setOnClickListener(v ->
                appendText(")"));

        // Clear
        findViewById(R.id.btnClear).setOnClickListener(v ->
                display.setText(""));

        // Backspace
        findViewById(R.id.btnBack).setOnClickListener(v -> {

            String text = display.getText().toString();

            if (!text.isEmpty()) {

                text = text.substring(0, text.length() - 1);

                display.setText(text);
            }
        });

        // Plus / Minus
        findViewById(R.id.btnPlusMinus).setOnClickListener(v -> {

            String text = display.getText().toString();

            if (text.isEmpty()) {
                appendText("-");
            } else if (!text.startsWith("-")) {
                display.setText("-" + text);
            } else {
                display.setText(text.substring(1));
            }
        });

        // Equal
        findViewById(R.id.btnEqual).setOnClickListener(v -> calculate());
    }

    // ----------------------------------------------------
    // SCIENTIFIC BUTTONS
    // ----------------------------------------------------

    private void setupScientificButtons() {

        // sin
        findViewById(R.id.btnSin).setOnClickListener(v ->
                appendFunction("sin("));

        // cos
        findViewById(R.id.btnCos).setOnClickListener(v ->
                appendFunction("cos("));

        // tan
        findViewById(R.id.btnTan).setOnClickListener(v ->
                appendFunction("tan("));

        // square root
        findViewById(R.id.btnSqrt).setOnClickListener(v ->
                appendFunction("sqrt("));

        // log
        findViewById(R.id.btnLog).setOnClickListener(v ->
                appendFunction("log("));

        // ln
        findViewById(R.id.btnLn).setOnClickListener(v ->
                appendFunction("ln("));

        // x²
        findViewById(R.id.btnSquare).setOnClickListener(v -> {

            if (!display.getText().toString().isEmpty()) {
                appendText("^2");
            }
        });

        // π
        findViewById(R.id.btnPi).setOnClickListener(v ->
                appendText("pi"));

        // e
        findViewById(R.id.btnE).setOnClickListener(v ->
                appendText("e"));
    }

    // ----------------------------------------------------
    // TEXT HELPERS
    // ----------------------------------------------------

    private void appendText(String value) {

        String current = display.getText().toString();

        display.setText(current + value);

        display.setSelection(display.length());
    }

    private void appendFunction(String function) {

        String current = display.getText().toString();

        // If previous character is a number, insert multiplication
        if (!current.isEmpty()) {

            char last = current.charAt(current.length() - 1);

            if (Character.isDigit(last) ||
                    last == ')' ||
                    last == 'e') {

                current += "*";
            }
        }

        display.setText(current + function);

        display.setSelection(display.length());
    }

    private void appendOperator(String operator) {

        String text = display.getText().toString();

        if (text.isEmpty()) {

            if (operator.equals("-")) {
                display.setText("-");
            }

            return;
        }

        char last = text.charAt(text.length() - 1);

        // Don't allow two operators together
        if (last == '+' ||
                last == '-' ||
                last == '*' ||
                last == '/' ||
                last == '^') {

            text = text.substring(0, text.length() - 1);
        }

        display.setText(text + operator);

        display.setSelection(display.length());
    }

    // ----------------------------------------------------
    // CALCULATE
    // ----------------------------------------------------

    private void calculate() {

        String expression = display.getText().toString();

        if (expression.isEmpty()) {
            return;
        }

        try {

            expression = expression
                    .replace("×", "*")
                    .replace("÷", "/")
                    .replace("−", "-");

            double result = new ExpressionParser(expression).parse();

            if (Double.isNaN(result) ||
                    Double.isInfinite(result)) {

                display.setText("Error");
                return;
            }

            display.setText(formatResult(result));

            display.setSelection(display.length());

        } catch (Exception e) {

            display.setText("Error");
        }
    }

    private String formatResult(double result) {

        if (result == Math.rint(result)) {
            return String.format(
                    Locale.US,
                    "%.0f",
                    result
            );
        }

        return String.format(
                        Locale.US,
                        "%.10f",
                        result
                ).replaceAll("0+$", "")
                .replaceAll("\\.$", "");
    }

    // ====================================================
    // EXPRESSION PARSER
    // ====================================================

    private static class ExpressionParser {

        private final String input;
        private int position = -1;
        private int character;

        ExpressionParser(String input) {
            this.input = input;
            nextCharacter();
        }

        private void nextCharacter() {

            position++;

            if (position < input.length()) {
                character = input.charAt(position);
            } else {
                character = -1;
            }
        }

        private boolean eat(int charToEat) {

            while (character == ' ') {
                nextCharacter();
            }

            if (character == charToEat) {
                nextCharacter();
                return true;
            }

            return false;
        }

        double parse() {

            double value = parseExpression();

            while (character == ' ') {
                nextCharacter();
            }

            if (character != -1) {
                throw new RuntimeException(
                        "Unexpected character"
                );
            }

            return value;
        }

        // ------------------------------------------------
        // + and -
        // ------------------------------------------------

        private double parseExpression() {

            double value = parseTerm();

            while (true) {

                if (eat('+')) {
                    value += parseTerm();

                } else if (eat('-')) {
                    value -= parseTerm();

                } else {
                    return value;
                }
            }
        }

        // ------------------------------------------------
        // * and /
        // ------------------------------------------------

        private double parseTerm() {

            double value = parsePower();

            while (true) {

                if (eat('*')) {

                    value *= parsePower();

                } else if (eat('/')) {

                    double divisor = parsePower();

                    if (divisor == 0) {
                        throw new ArithmeticException(
                                "Division by zero"
                        );
                    }

                    value /= divisor;

                } else {

                    return value;
                }
            }
        }

        // ------------------------------------------------
        // POWER
        // ------------------------------------------------

        private double parsePower() {

            double value = parseUnary();

            if (eat('^')) {

                double exponent = parsePower();

                value = Math.pow(value, exponent);
            }

            return value;
        }

        // ------------------------------------------------
        // UNARY + -
        // ------------------------------------------------

        private double parseUnary() {

            if (eat('+')) {
                return parseUnary();
            }

            if (eat('-')) {
                return -parseUnary();
            }

            return parsePostfix();
        }

        // ------------------------------------------------
        // PERCENTAGE
        // ------------------------------------------------

        private double parsePostfix() {

            double value = parsePrimary();

            while (eat('%')) {
                value = value / 100.0;
            }

            return value;
        }

        // ------------------------------------------------
        // NUMBERS / FUNCTIONS / BRACKETS
        // ------------------------------------------------

        private double parsePrimary() {

            while (character == ' ') {
                nextCharacter();
            }

            // Parentheses
            if (eat('(')) {

                double value = parseExpression();

                if (!eat(')')) {
                    throw new RuntimeException(
                            "Missing )"
                    );
                }

                return value;
            }

            // Functions
            if (character >= 'a' && character <= 'z') {

                StringBuilder function =
                        new StringBuilder();

                while ((character >= 'a' &&
                        character <= 'z')) {

                    function.append(
                            (char) character
                    );

                    nextCharacter();
                }

                String name = function.toString();

                if (name.equals("pi")) {
                    return Math.PI;
                }

                if (name.equals("e")) {
                    return Math.E;
                }

                if (!eat('(')) {
                    throw new RuntimeException(
                            "Expected ("
                    );
                }

                double argument =
                        parseExpression();

                if (!eat(')')) {
                    throw new RuntimeException(
                            "Missing )"
                    );
                }

                return applyFunction(
                        name,
                        argument
                );
            }

            // Number
            StringBuilder number =
                    new StringBuilder();

            while ((character >= '0' &&
                    character <= '9') ||
                    character == '.') {

                number.append(
                        (char) character
                );

                nextCharacter();
            }

            if (number.length() == 0) {
                throw new RuntimeException(
                        "Expected number"
                );
            }

            return Double.parseDouble(
                    number.toString()
            );
        }

        // ------------------------------------------------
        // SCIENTIFIC FUNCTIONS
        // ------------------------------------------------

        private double applyFunction(
                String name,
                double value) {

            switch (name) {

                case "sin":
                    return Math.sin(
                            Math.toRadians(value)
                    );

                case "cos":
                    return Math.cos(
                            Math.toRadians(value)
                    );

                case "tan":
                    return Math.tan(
                            Math.toRadians(value)
                    );

                case "sqrt":

                    if (value < 0) {
                        throw new ArithmeticException(
                                "Invalid square root"
                        );
                    }

                    return Math.sqrt(value);

                case "log":

                    if (value <= 0) {
                        throw new ArithmeticException(
                                "Invalid log"
                        );
                    }

                    return Math.log10(value);

                case "ln":

                    if (value <= 0) {
                        throw new ArithmeticException(
                                "Invalid ln"
                        );
                    }

                    return Math.log(value);

                default:

                    throw new RuntimeException(
                            "Unknown function: " + name
                    );
            }
        }
    }
}
```
## Activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="10dp"
    android:background="#17171C">

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="SCIENTIFIC CALCULATOR"
        android:textColor="#FFFFFF"
        android:textSize="21sp"
        android:textStyle="bold"
        android:gravity="center"
        android:padding="6dp"/>

    <EditText
        android:id="@+id/display"
        android:layout_width="match_parent"
        android:layout_height="82dp"
        android:background="#25252D"
        android:textColor="#FFFFFF"
        android:textColorHint="#777777"
        android:textSize="28sp"
        android:gravity="right|center_vertical"
        android:padding="15dp"
        android:inputType="none"
        android:focusable="false"
        android:hint="0"
        android:layout_marginBottom="6dp"/>

    <!-- Row 1 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnSin"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="sin"
            android:textSize="14sp"/>

        <Button
            android:id="@+id/btnCos"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="cos"
            android:textSize="14sp"/>

        <Button
            android:id="@+id/btnTan"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="tan"
            android:textSize="14sp"/>

        <Button
            android:id="@+id/btnSqrt"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="√"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnLog"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="log"
            android:textSize="13sp"/>

    </LinearLayout>

    <!-- Row 2 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnLn"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="ln"
            android:textSize="14sp"/>

        <Button
            android:id="@+id/btnSquare"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="x²"
            android:textSize="16sp"/>

        <Button
            android:id="@+id/btnPower"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="xʸ"
            android:textSize="16sp"/>

        <Button
            android:id="@+id/btnPi"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="π"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnE"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="e"
            android:textSize="18sp"/>

    </LinearLayout>

    <!-- Row 3 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnPercent"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="%"/>

        <Button
            android:id="@+id/btnOpen"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="("
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnClose"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text=")"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnClear"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="C"
            android:textStyle="bold"/>

        <Button
            android:id="@+id/btnBack"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="⌫"
            android:textSize="18sp"/>

    </LinearLayout>

    <!-- Row 4 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn7"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="7"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn8"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="8"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn9"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="9"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnDivide"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="÷"
            android:textSize="20sp"/>

    </LinearLayout>

    <!-- Row 5 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn4"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="4"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn5"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="5"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn6"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="6"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnMultiply"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="×"
            android:textSize="20sp"/>

    </LinearLayout>

    <!-- Row 6 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btn1"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="1"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn2"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="2"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn3"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="3"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnMinus"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="−"
            android:textSize="20sp"/>

    </LinearLayout>

    <!-- Row 7 -->
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:orientation="horizontal">

        <Button
            android:id="@+id/btnPlusMinus"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="±"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btn0"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="0"
            android:textSize="18sp"/>

        <Button
            android:id="@+id/btnDot"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="."
            android:textSize="20sp"/>

        <Button
            android:id="@+id/btnPlus"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:text="+"
            android:textSize="20sp"/>

    </LinearLayout>

    <!-- Equal -->
    <Button
        android:id="@+id/btnEqual"
        android:layout_width="match_parent"
        android:layout_height="52dp"
        android:text="="
        android:textSize="22sp"
        android:textStyle="bold"
        android:layout_marginTop="4dp"/>

</LinearLayout>
```

## Output

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/bd45979b-f81d-497b-b991-a225a9c2db74" />

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/3bd985cd-d7f7-48eb-b969-eec8fab8d8b8" />

## Result
Thus, a fully functional Scientific Calculator Android Application was developed successfully. The application performs basic arithmetic and scientific calculations including trigonometric functions, square root, logarithms, powers, percentages, constants, and bracketed expressions with proper calculation and error handling.
