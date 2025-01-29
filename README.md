import java.util.*;
import java.math.BigDecimal;
import java.math.BigInteger;
import java.math.RoundingMode;

public class ShamirSecretSharing {
    public static void main(String[] args) {
        // JSON content as a string (since JDoodle doesn't support file reading)
        String jsonContent = "{ \"keys\": { \"n\": 4, \"k\": 3 }, \"1\": { \"base\": \"10\", \"value\": \"4\" }, \"2\": { \"base\": \"2\", \"value\": \"111\" }, \"3\": { \"base\": \"10\", \"value\": \"12\" }, \"6\": { \"base\": \"4\", \"value\": \"213\" } }";
        
        // Extract values manually
        int k = 3;
        Map<Integer, BigDecimal> points = new HashMap<>();
        points.put(1, new BigDecimal(new BigInteger("4", 10)));
        points.put(2, new BigDecimal(new BigInteger("111", 2)));
        points.put(3, new BigDecimal(new BigInteger("12", 10)));
        points.put(6, new BigDecimal(new BigInteger("213", 4)));
        
        // Convert map to list of points
        List<BigDecimal[]> pointList = new ArrayList<>();
        for (Map.Entry<Integer, BigDecimal> entry : points.entrySet()) {
            pointList.add(new BigDecimal[]{new BigDecimal(entry.getKey()), entry.getValue()});
        }
        
        // Solve for constant term using Lagrange Interpolation
        BigDecimal secret = lagrangeInterpolation(pointList, k);
        System.out.println("Secret (c): " + secret.setScale(0, RoundingMode.HALF_UP));
    }
    
    public static BigDecimal lagrangeInterpolation(List<BigDecimal[]> points, int k) {
        BigDecimal result = BigDecimal.ZERO;
        for (int i = 0; i < k; i++) {
            BigDecimal term = points.get(i)[1]; // y_i
            for (int j = 0; j < k; j++) {
                if (i != j) {
                    BigDecimal numerator = BigDecimal.ZERO.subtract(points.get(j)[0]);
                    BigDecimal denominator = points.get(i)[0].subtract(points.get(j)[0]);
                    term = term.multiply(numerator.divide(denominator, 50, RoundingMode.HALF_UP));
                }
            }
            result = result.add(term);
        }
        return result;
    }
}
