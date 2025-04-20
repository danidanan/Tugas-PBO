# Tugas-PBO2
import java.util.*;

public class investasiapp {
    static Scanner scanner = new Scanner(System.in);
    static Map<String, user> users = new HashMap<>();
    static List<Saham> sahamList = new ArrayList<>();
    static List<SBN> sbnList = new ArrayList<>();

    public static void main(String[] args) {
        seedData();
        while (true) {
            System.out.println("\n=== MENU UTAMA ===");
            System.out.println("1. Login");
            System.out.println("0. Keluar");
            System.out.print("Pilih: ");
            int pilih = scanner.nextInt();
            scanner.nextLine();

            if (pilih == 1) {
                login();
            } else if (pilih == 0) {
                System.out.println("Terima kasih telah menggunakan aplikasi.");
                break;
            }
        }
    }
