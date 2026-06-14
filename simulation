#include <iostream>
#include <vector>
#include <fstream>
#include <memory>
#include <string>
#include <cstdlib>
#include <iomanip>


class PhysiologicalFactor {
protected:
    std::string name;
    double depletionRateModifier;

public:
    PhysiologicalFactor(std::string n, double rate) : name(n), depletionRateModifier(rate) {}
    virtual ~PhysiologicalFactor() = default;
    
    std::string getName() const { return name; }
    
    
    virtual double calculateNextCapacity(double currentCapacity, double baseDepletion) {
        double reduction = baseDepletion * depletionRateModifier;
        return (currentCapacity - reduction > 0) ? currentCapacity - reduction : 0.0;
    }
};


class Estrogen : public PhysiologicalFactor {
public:
    Estrogen() : PhysiologicalFactor("Estrogen", 2.5) {}
    double calculateNextCapacity(double currentCapacity, double baseDepletion) override {
        
        double nonLinearExhaustion = baseDepletion * depletionRateModifier * (1.0 + (100.0 - currentCapacity) / 100.0);
        return (currentCapacity - nonLinearExhaustion > 0) ? currentCapacity - nonLinearExhaustion : 0.0;
    }
};

class ThyroidHormone : public PhysiologicalFactor {
public:
    ThyroidHormone() : PhysiologicalFactor("Thyroid_T3", 1.8) {}
};

class FGFR3 : public PhysiologicalFactor {
public:
    FGFR3() : PhysiologicalFactor("FGFR3", 1.5) {}
};

class Somatostatin : public PhysiologicalFactor {
public:
    Somatostatin() : PhysiologicalFactor("Somatostatin", 0.6) {}
};

class Control : public PhysiologicalFactor {
    
public:
    Control() : PhysiologicalFactor("Control_Baseline", 1.0) {}
};


void printReferences() {
    std::cout << "\n======================================================\n";
    std::cout << "      SOURCE DATA & REFERENCES (PubMed IDs)           \n";
    std::cout << "======================================================\n";
    std::cout << "[*] Estrogen     : PMID 24708243\n";
    std::cout << "    - Mechanism: Irreversibly depletes resting zone progenitor cells.\n\n";
    std::cout << "[*] Thyroid (T3) : PMID 26055531\n";
    std::cout << "    - Mechanism: Accelerates hypertrophic differentiation and bone cell invasion.\n\n";
    std::cout << "[*] FGFR3        : PMID 19204052\n";
    std::cout << "    - Mechanism: Inhibits chondrocyte proliferation via MAPK signaling.\n\n";
    std::cout << "[*] Somatostatin : PMID 25905282\n";
    std::cout << "    - Mechanism: Inhibits GH/IGF-1 axis, delaying normal maturation.\n";
    std::cout << "======================================================\n\n";
}


void runSimulation() {
    const double INITIAL_CAPACITY = 100.0; 
    const double BASE_DEPLETION_RATE = 1.2; 
    const int SIMULATION_MONTHS = 100;

    std::vector<std::unique_ptr<PhysiologicalFactor>> factors;
    factors.push_back(std::make_unique<Control>());
    factors.push_back(std::make_unique<Estrogen>());
    factors.push_back(std::make_unique<ThyroidHormone>());
    factors.push_back(std::make_unique<FGFR3>());
    factors.push_back(std::make_unique<Somatostatin>());

    std::ofstream csvFile("epiphyseal_data.csv");
    
    
    csvFile << "Time_Months";
    for (const auto& factor : factors) {
        csvFile << "," << factor->getName();
    }
    csvFile << "\n";

    
    std::vector<double> capacities(factors.size(), INITIAL_CAPACITY);

    
    for (int month = 0; month <= SIMULATION_MONTHS; ++month) {
        csvFile << month;
        for (size_t i = 0; i < factors.size(); ++i) {
            csvFile << "," << std::fixed << std::setprecision(2) << capacities[i];
            capacities[i] = factors[i]->calculateNextCapacity(capacities[i], BASE_DEPLETION_RATE);
        }
        csvFile << "\n";
    }
    csvFile.close();
    std::cout << "[+] Data successfully exported to epiphyseal_data.csv\n";
}


void generateAndRunGraph() {
    std::ofstream pyScript("plot_fusion.py");
    pyScript << "import pandas as pd\n"
             << "import matplotlib.pyplot as plt\n"
             << "import os\n\n"
             << "try:\n"
             << "    data = pd.read_csv('epiphyseal_data.csv')\n"
             << "    plt.figure(figsize=(12, 7))\n"
             << "    colors = {'Control_Baseline':'black', 'Estrogen':'magenta', 'Thyroid_T3':'orange', 'FGFR3':'red', 'Somatostatin':'blue'}\n"
             << "    for col in data.columns[1:]:\n"
             << "        plt.plot(data['Time_Months'], data[col], label=col, color=colors.get(col), linewidth=2.5)\n"
             << "    plt.axhline(0, color='gray', linestyle='--')\n"
             << "    plt.title('Effects of Biological Factors on Epiphyseal Progenitor Exhaustion (Fusion)', fontsize=14)\n"
             << "    plt.xlabel('Time (Simulated Months)', fontsize=12)\n"
             << "    plt.ylabel('Remaining Progenitor Capacity (%)', fontsize=12)\n"
             << "    plt.legend()\n"
             << "    plt.grid(True, alpha=0.3)\n"
             << "    plt.savefig('Epiphyseal_Fusion_Graph.png')\n"
             << "    print('[+] Graph successfully generated: Epiphyseal_Fusion_Graph.png')\n"
             << "except Exception as e:\n"
             << "    print(f'Error generating graph: {e}')\n";
    pyScript.close();

    std::cout << "[+] Launching graphing engine...\n";
    int result = system("python plot_fusion.py || python3 plot_fusion.py");
    if (result != 0) {
        std::cerr << "[-] Failed to execute Python plotting script. Ensure matplotlib is installed.\n";
    }
}

int main() {
    printReferences();
    std::cout << "Starting Epiphyseal Plate Fusion Simulation...\n";
    runSimulation();
    generateAndRunGraph();
    return 0;
}
