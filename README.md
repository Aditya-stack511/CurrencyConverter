# CurrencyConverter
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.HashMap;
import java.util.Scanner;
import org.json.JSONObject;

public class CurrencyConverter {

    public static void main(String[] args) throws IOException {
      
        // Currency codes mapping
        HashMap<Integer, String> currencycodes = new HashMap<>();
         
        // Add currency codes to the map
        currencycodes.put(1, "USD"); // US Dollar
        currencycodes.put(2, "CAD"); // Canadian Dollar
        currencycodes.put(3, "EUR"); // Euro
        currencycodes.put(4, "HKD"); // Hong Kong Dollar
        currencycodes.put(5, "INR"); // Indian Rupees
        
        String fromcode, tocode;
        double amount;
        
        Scanner sc = new Scanner(System.in);
        
        // Welcome message
        System.out.println("Welcome to the currency converter!");
        
        // Asking the user to choose the currency to convert FROM
        System.out.println("Currency converting FROM?");
        System.out.print("1:USD (US Dollar) \t 2:CAD (Canadian Dollar) \t 3:EUR (Euro) \t 4:HKD (Hong Kong Dollar) \t 5:INR (Indian Rupees): ");
        int fromChoice = sc.nextInt();
        fromcode = currencycodes.get(fromChoice); // Get the currency code from the map based on user's choice
        
        // Asking the user to choose the currency to convert TO
        System.out.println("Currency converting TO?");
        System.out.print("1:USD (US Dollar) \t 2:CAD (Canadian Dollar) \t 3:EUR (Euro) \t 4:HKD (Hong Kong Dollar) \t 5:INR (Indian Rupees): ");
        int toChoice = sc.nextInt();
        tocode = currencycodes.get(toChoice); // Get the currency code for the target currency
        
        // Ask for the amount to convert
        System.out.println("Amount you wish to convert?");
        amount = sc.nextDouble();
        
        // Call method to perform currency conversion
        sendHttpGETRequest(fromcode, tocode, amount);
        
        // Thank the user for using the converter
        System.out.println("Thank you for using the currency converter!");
    }

    // Method to send HTTP GET request and perform conversion
    private static void sendHttpGETRequest(String fromcode, String tocode, double amount) throws IOException {
        
        // Construct the API URL to get exchange rates
        String GETURL = "https://api.exchangeratesapi.io/latest?base=" + fromcode + "&symbols=" + tocode;
        
        // Create a URL object with the constructed URL
        URL url = new URL(GETURL);
        HttpURLConnection httpURLConnection = (HttpURLConnection) url.openConnection();
        httpURLConnection.setRequestMethod("GET"); // Set the request method to GET
        
        // Get the response code from the API request
        int responsecode = httpURLConnection.getResponseCode();
        
        if (responsecode == HttpURLConnection.HTTP_OK) {
            // If the response code is OK (200), process the response
            BufferedReader in = new BufferedReader(new InputStreamReader(httpURLConnection.getInputStream()));
            String inputLine;
            StringBuffer response = new StringBuffer();
            
            // Read the response from the API
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
            in.close();
            
            // Parse the JSON response to get the exchange rate
            JSONObject obj = new JSONObject(response.toString());
            Double exchangeRate = obj.getJSONObject("rates").getDouble(tocode);
            
            // Output the exchange rate and converted amount
            System.out.println("Exchange rate: " + exchangeRate);
            System.out.println(amount + " " + fromcode + " = " + (amount * exchangeRate) + " " + tocode);
        } else {
            // If the request failed, print an error message
            System.out.println("GET request failed with response code: " + responsecode);
        }
    }
}
