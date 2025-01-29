function decodeBaseValue(base, value) {
    return BigInt(parseInt(value, parseInt(base)));
}

function lagrangeInterpolation(xValues, yValues, k) {
    let secret = BigInt(0);

    for (let i = 0; i < k; i++) {
        let term = yValues[i];

        for (let j = 0; j < k; j++) {
            if (i !== j) {
                let numerator = BigInt(-xValues[j]);
                let denominator = BigInt(xValues[i] - xValues[j]);
                term = term * numerator / denominator;
            }
        }

        secret += term;
    }

    return secret % BigInt(256); 
}


function calculateSecret(jsonInput) {
    const data = JSON.parse(jsonInput);
    const keys = data.keys;
    const n = keys.n;
    const k = keys.k;

    let xValues = [];
    let yValues = [];

    let i = 0;
    for (const key in data) {
        if (key === "keys") continue;

        const root = data[key];
        const base = root.base;
        const value = root.value;


        const decodedValue = decodeBaseValue(base, value);
        const xValue = parseInt(key);

       
        xValues.push(xValue);
        yValues.push(decodedValue);

        i++;


        if (i >= k) break;
    }

   
    const secret = lagrangeInterpolation(xValues, yValues, k);


    return secret.toString(); 
}

//Input 1
const jsonInput1 = `
{
    "keys": {
        "n": 4,
        "k": 3
    },
    "1": {
        "base": "10",
        "value": "4"
    },
    "2": {
        "base": "2",
        "value": "111"
    },
    "3": {
        "base": "10",
        "value": "12"
    },
    "6": {
        "base": "4",
        "value": "213"
    }
}
`;

// Input 2
const jsonInput2 = `
{
"keys": {
    "n": 10,
    "k": 7
  },
  "1": {
    "base": "6",
    "value": "13444211440455345511"
  },
  "2": {
    "base": "15",
    "value": "aed7015a346d63"
  },
  "3": {
    "base": "15",
    "value": "6aeeb69631c227c"
  },
  "4": {
    "base": "16",
    "value": "e1b5e05623d881f"
  },
  "5": {
    "base": "8",
    "value": "316034514573652620673"
  },
  "6": {
    "base": "3",
    "value": "2122212201122002221120200210011020220200"
  },
  "7": {
    "base": "3",
    "value": "20120221122211000100210021102001201112121"
  },
  "8": {
    "base": "6",
    "value": "20220554335330240002224253"
  },
  "9": {
    "base": "12",
    "value": "45153788322a1255483"
  },
  "10": {
    "base": "7",
    "value": "1101613130313526312514143"
  }
}
`;


console.log("Secret for Sample Input 1: " + calculateSecret(jsonInput1));
console.log("Secret for Sample Input 2: " + calculateSecret(jsonInput2));
