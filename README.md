# catalog-assignment
import java.util.*;
import org.json.simple.JSONObject;
import org.json.simple.parser.JSONParser;
import java.io.FileReader;

public class ShamirSecretRecovery {
    public static void main(String[] args) {
        try {
            // JSON Parsing
            JSONParser parser = new JSONParser();
            JSONObject jsonObject = (JSONObject) parser.parse(new FileReader("input.json"));

            int n = Integer.parseInt(jsonObject.get("n").toString());
            int k = Integer.parseInt(jsonObject.get("k").toString());

           
            List<Point> points = new ArrayList<>();

           
            for (int i = 1; i <= n; i++) {
                JSONObject rootData = (JSONObject) jsonObject.get(String.valueOf(i));
                
                int x = i;
                int base = Integer.parseInt(rootData.get("base").toString());
                String valueStr = rootData.get("value").toString();
                
                int y = decodeValue(valueStr, base);
                points.add(new Point(x, y));
            }

            int secret = recoverSecret(points, k);
            System.out.println("Secret: " + secret);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    static int decodeValue(String value, int base) {
        return Integer.parseInt(value, base);
    }
    static int recoverSecret(List<Point> points, int k) {
        int secret = 0;
        
        for (int i = 0; i < k; i++) {
            int lagrangeTerm = calculateLagrangeTerm(points, i);
            secret += lagrangeTerm;
        }
        
        return secret;
    }
    static int calculateLagrangeTerm(List<Point> points, int index) {
        Point currentPoint = points.get(index);
        int numerator = 1;
        int denominator = 1;
        
        for (int j = 0; j < points.size(); j++) {
            if (j != index) {
                Point otherPoint = points.get(j);
                numerator *= -otherPoint.x;
                denominator *= (currentPoint.x - otherPoint.x);
            }
        }
        
        return (currentPoint.y * numerator) / denominator;
    }
    static class Point {
        int x, y;
        
        Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }
}
