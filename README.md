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

    static void seedData() {
        users.put("admin", new admin("admin", "admin123"));
        users.put("cust", new Customer("cust", "cust123"));

        sahamList.add(new Saham("BBCA", "Bank BCA", 9000));
        sahamList.add(new Saham("TLKM", "Telkom Indonesia", 4500));
        sahamList.add(new Saham("BRII", "Bank BRI", 5500));
        sahamList.add(new Saham("SHPE", "Shopee Indonesia", 8000));
        sahamList.add(new Saham("GJK", "Gojek Indonesia", 7500));
        sbnList.add(new SBN("ORI021", 6.25, 36, "2028-01-01", 1000000000));
        sbnList.add(new SBN("ORI023", 6.25, 36, "2028-01-01", 1000000000));
        sbnList.add(new SBN("STO10", 6.25, 36, "2028-01-01", 1000000000));
    }

    static void login() {
        System.out.print("Username: ");
        String username = scanner.nextLine();
        System.out.print("Password: ");
        String password = scanner.nextLine();

        user user = users.get(username);
        if (user != null && user.checkPassword(password)) {
            user.menu(sahamList, sbnList);
        } else {
            System.out.println("Login gagal!");
        }
    }
}

abstract class user {
    protected String username;
    protected String password;

    user(String username, String password) {
        this.username = username;
        this.password = password;
    }

    boolean checkPassword(String input) {
        return this.password.equals(input);
    }

    abstract void menu(List<Saham> sahamList, List<SBN> sbnList);
}

class admin extends user {
    admin(String username, String password) {
        super(username, password);
    }

    @Override
    void menu(List<Saham> sahamList, List<SBN> sbnList) {
        while (true) {
            System.out.println("\n=== MENU ADMIN ===");
            System.out.println("1. Tambah Saham");
            System.out.println("2. Ubah Harga Saham");
            System.out.println("3. Tambah SBN");
            System.out.println("4. Logout");
            System.out.print("Pilih: ");
            int pilih = investasiapp.scanner.nextInt();
            investasiapp.scanner.nextLine();

            switch (pilih) {
                case 1 -> {
                    System.out.print("Kode: ");
                    String kode = investasiapp.scanner.nextLine();
                    System.out.print("Nama Perusahaan: ");
                    String nama = investasiapp.scanner.nextLine();
                    System.out.print("Harga: ");
                    double harga = investasiapp.scanner.nextDouble();
                    sahamList.add(new Saham(kode, nama, harga));
                    System.out.println("✅ Saham ditambahkan.");
                }
                case 2 -> {
                    System.out.print("Kode Saham: ");
                    String kode = investasiapp.scanner.nextLine();
                    for (Saham s : sahamList) {
                        if (s.kode.equalsIgnoreCase(kode)) {
                            System.out.print("Harga baru: ");
                            s.harga = investasiapp.scanner.nextDouble();
                            System.out.println("✅ Harga diubah.");
                        }
                    }
                }
                case 3 -> {
                    System.out.print("Nama SBN: ");
                    String nama = investasiapp.scanner.nextLine();
                    System.out.print("Bunga (%): ");
                    double bunga = investasiapp.scanner.nextDouble();
                    System.out.print("Jangka Waktu (bulan): ");
                    int waktu = investasiapp.scanner.nextInt();
                    investasiapp.scanner.nextLine();
                    System.out.print("Tanggal Jatuh Tempo: ");
                    String tempo = investasiapp.scanner.nextLine();
                    System.out.print("Kuota Nasional: ");
                    double kuota = investasiapp.scanner.nextDouble();
                    sbnList.add(new SBN(nama, bunga, waktu, tempo, kuota));
                    System.out.println("✅ SBN ditambahkan.");
                }
                case 4 -> {
                    return;
                }
            }
        }
    }
}
