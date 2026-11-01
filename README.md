import numpy as np
import matplotlib.pyplot as plt
from matplotlib.patches import FancyBboxPatch
from sklearn.tree import DecisionTreeClassifier

# ---------------------------------------
# 1. CREATE TRAINING DATA
# ---------------------------------------
np.random.seed(42)

voltage = np.linspace(150, 280, 1000).reshape(-1, 1)

# Labels: 0 = Low, 1 = Normal, 2 = High
labels = np.where(
    voltage.ravel() < 220, 0,
    np.where(voltage.ravel() <= 240, 1, 2)
)

# ---------------------------------------
# 2. TRAIN MACHINE LEARNING MODEL
# ---------------------------------------
model = DecisionTreeClassifier(max_depth=3)
model.fit(voltage, labels)

target_voltage = 230

# ---------------------------------------
# 3. DRAW SIX-STAGE DIAGRAM
# ---------------------------------------
def show_flowchart():
    fig, ax = plt.subplots(figsize=(10, 9))
    ax.set_xlim(0, 10)
    ax.set_ylim(0, 12)
    ax.axis("off")

    stages = [
        ("1. INPUT VOLTAGE",
         "Enter measured voltage in volts"),
        ("2. DATA PROCESSING",
         "Validate and prepare the data"),
        ("3. MACHINE LEARNING",
         "Train the Decision Tree model"),
        ("4. VOLTAGE PREDICTION",
         "Predict low, normal, or high"),
        ("5. CONTROL ACTION",
         "Recommend voltage correction"),
        ("6. OUTPUT DISPLAY",
         "Show prediction and result graph")
    ]

    colors = [
        "lightblue", "lightgreen", "plum",
        "wheat", "lightcoral", "turquoise"
    ]

    y_positions = [10, 8.2, 6.4, 4.6, 2.8, 1.0]

    for i, (title, description) in enumerate(stages):
        y = y_positions[i]

        box = FancyBboxPatch(
            (1.5, y - 0.45), 7, 1.1,
            boxstyle="round,pad=0.15",
            facecolor=colors[i],
            edgecolor="black",
            linewidth=1.5
        )
        ax.add_patch(box)

        ax.text(
            5, y + 0.18, title,
            ha="center", va="center",
            fontsize=12, fontweight="bold"
        )
        ax.text(
            5, y - 0.15, description,
            ha="center", va="center", fontsize=10
        )

        if i < len(stages) - 1:
            ax.annotate(
                "",
                xy=(5, y_positions[i + 1] + 0.7),
                xytext=(5, y - 0.5),
                arrowprops=dict(arrowstyle="->", lw=2)
            )

    ax.set_title(
        "INTELLIGENT AUTOMATIC VOLTAGE REGULATOR\n"
        "Using Machine Learning",
        fontsize=15, fontweight="bold", pad=15
    )

    plt.tight_layout()
    plt.show()


# ---------------------------------------
# 4. GET INPUT VOLTAGE
# ---------------------------------------
def run_regulator():
    show_flowchart()

    try:
        input_voltage = float(
            input("Enter input voltage (150-280 V): ")
        )

        if not 150 <= input_voltage <= 280:
            print("Please enter a voltage from 150 to 280 V.")
            return

    except ValueError:
        print("Invalid input! Enter a numeric voltage.")
        return

    # -----------------------------------
    # 5. PREDICT VOLTAGE CONDITION
    # -----------------------------------
    prediction = model.predict([[input_voltage]])[0]

    conditions = {
        0: "LOW VOLTAGE",
        1: "NORMAL VOLTAGE",
        2: "HIGH VOLTAGE"
    }

    actions = {
        0: "Increase voltage toward 230 V.",
        1: "Maintain voltage near the target.",
        2: "Decrease voltage toward 230 V."
    }

    condition = conditions[prediction]
    action = actions[prediction]

    # -----------------------------------
    # 6. DISPLAY TEXT OUTPUT
    # -----------------------------------
    print("\n========== REGULATOR OUTPUT ==========")
    print(f"Input Voltage : {input_voltage:.2f} V")
    print(f"Target Voltage: {target_voltage:.2f} V")
    print(f"Prediction    : {condition}")
    print(f"Control Action: {action}")
    print("======================================")

    # -----------------------------------
    # 7. DISPLAY RESULT GRAPH
    # -----------------------------------
    fig, ax = plt.subplots(figsize=(9, 5))

    ax.bar(
        ["Input Voltage", "Target Voltage"],
        [input_voltage, target_voltage],
        color=["orange", "green"],
        width=0.5
    )

    ax.axhline(
        220, color="red", linestyle="--",
        label="Lower normal limit (220 V)"
    )
    ax.axhline( Enter input voltage: 270

--- VOLTAGE REGULATOR OUTPUT ---
Input Voltage: 270.0 V
Predicted Condition: High
Control Action: Reduce voltage

        240, color="blue", linestyle="--",
        label="Upper normal limit (240 V)"
    )

    ax.set_ylim(0, max(300, input_voltage + 20))
    ax.set_ylabel("Voltage (V)")
    ax.set_title(
        f"Voltage Regulator Result: {condition}\n{action}"
    )
    ax.grid(axis="y", alpha=0.3)
    ax.legend()

    for i, value in enumerate([input_voltage, target_voltage]):
        ax.text(i, value + 3, f"{value:.1f} V", ha="center")

    plt.tight_layout()
    plt.show()


if __name__ == "__main__":
    run_regulator()
