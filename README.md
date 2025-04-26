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
class Customer extends user {
    Map<String, Integer> sahamOwned = new HashMap<>();
    Map<String, Double> sbnOwned = new HashMap<>();

    Customer(String username, String password) {
        super(username, password);
    }

    @Override
    void menu(List<Saham> sahamList, List<SBN> sbnList) {
        while (true) {
            System.out.println("\n=== MENU CUSTOMER ===");
            System.out.println("1. Beli Saham");
            System.out.println("2. Jual Saham");
            System.out.println("3. Beli SBN");
            System.out.println("4. Simulasi Kupon SBN");
            System.out.println("5. Lihat Portofolio");
            System.out.println("6. Logout");
            System.out.print("Pilih: ");
            int pilih = investasiapp.scanner.nextInt();
            investasiapp.scanner.nextLine();

            switch (pilih) {
                case 1 -> beliSaham(sahamList);
                case 2 -> jualSaham(sahamList);
                case 3 -> beliSBN(sbnList);
                case 4 -> simulasiSBN(sbnList);
                case 5 -> lihatPortofolio(sahamList, sbnList);
                case 6 -> {
                    return;
                }
            }
        }
    }

    void beliSaham(List<Saham> sahamList) {
        System.out.println("\n=== DAFTAR SAHAM ===");
        for (int i = 0; i < sahamList.size(); i++) {
            Saham s = sahamList.get(i);
            System.out.printf("%d. %s (%s) - Rp %.2f\n", i + 1, s.nama, s.kode, s.harga);
        }

        System.out.print("Pilih nomor saham: ");
        int index = investasiapp.scanner.nextInt() - 1;
        if (index < 0 || index >= sahamList.size()) {
            System.out.println("Pilihan tidak valid.");
            return;
        }

        Saham s = sahamList.get(index);
        System.out.print("Jumlah lembar: ");
        int jumlah = investasiapp.scanner.nextInt();
        sahamOwned.put(s.kode, sahamOwned.getOrDefault(s.kode, 0) + jumlah);
        System.out.println("✅ Saham dibeli.");
    }

    void jualSaham(List<Saham> sahamList) {
        if (sahamOwned.isEmpty()) {
            System.out.println("⚠️ Anda belum memiliki saham.");
            return;
        }

        System.out.println("\n=== SAHAM DIMILIKI ===");
        List<String> kodeList = new ArrayList<>(sahamOwned.keySet());
        for (int i = 0; i < kodeList.size(); i++) {
            String kode = kodeList.get(i);
            System.out.printf("%d. %s - %d lembar\n", i + 1, kode, sahamOwned.get(kode));
        }

        System.out.print("Pilih nomor saham: ");
        int index = investasiapp.scanner.nextInt() - 1;
        if (index < 0 || index >= kodeList.size()) {
            System.out.println("Pilihan tidak valid.");
            return;
        }

        String kode = kodeList.get(index);
        System.out.print("Jumlah yang dijual: ");
        int jumlah = investasiapp.scanner.nextInt();
        int dimiliki = sahamOwned.get(kode);

        if (jumlah > dimiliki) {
            System.out.println("❌ Jumlah melebihi kepemilikan.");
        } else {
            sahamOwned.put(kode, dimiliki - jumlah);
            if (sahamOwned.get(kode) == 0) sahamOwned.remove(kode);
            System.out.println("✅ Saham dijual.");
        }
    }

    void beliSBN(List<SBN> sbnList) {
        System.out.println("\n=== DAFTAR SBN ===");
        for (int i = 0; i < sbnList.size(); i++) {
            SBN s = sbnList.get(i);
            System.out.printf("%d. %s - Bunga: %.2f%% - Kuota: Rp %.2f\n", i + 1, s.nama, s.bunga, s.kuotaNasional);
        }

        System.out.print("Pilih nomor SBN: ");
        int index = investasiapp.scanner.nextInt() - 1;
        SBN s = sbnList.get(index);
        System.out.print("Nominal pembelian: ");
        double nominal = investasiapp.scanner.nextDouble();

        if (nominal > s.kuotaNasional) {
            System.out.println("❌ Kuota tidak cukup.");
        } else {
            sbnOwned.put(s.nama, sbnOwned.getOrDefault(s.nama, 0.0) + nominal);
            s.kuotaNasional -= nominal;
            System.out.println("✅ SBN dibeli.");
        }
    }

    void simulasiSBN(List<SBN> sbnList) {
        System.out.println("\n=== SIMULASI KUPON SBN ===");
        for (String nama : sbnOwned.keySet()) {
            double nominal = sbnOwned.get(nama);
            for (SBN s : sbnList) {
                if (s.nama.equals(nama)) {
                    double kupon = s.bunga / 12 / 100 * 0.9 * nominal;
                    System.out.printf("%s: Kupon/bulan Rp %.2f\n", nama, kupon);
                }
            }
        }
    }
  void lihatPortofolio(List<Saham> sahamList, List<SBN> sbnList) {
        System.out.println("\n=== PORTOFOLIO ===");
        double totalSaham = 0, totalKupon = 0;

        System.out.println(">> SAHAM:");
        for (String kode : sahamOwned.keySet()) {
            int lembar = sahamOwned.get(kode);
            for (Saham s : sahamList) {
                if (s.kode.equals(kode)) {
                    double total = s.harga * lembar;
                    totalSaham += total;
                    System.out.printf("%s - %d lembar - Nilai: Rp %.2f\n", s.nama, lembar, total);
                }
            }
        }

        System.out.printf("Total Investasi Saham: Rp %.2f\n", totalSaham);

        System.out.println("\n>> SBN:");
        for (String nama : sbnOwned.keySet()) {
            double nominal = sbnOwned.get(nama);
            for (SBN s : sbnList) {
                if (s.nama.equals(nama)) {
                    double kupon = s.bunga / 12 / 100 * 0.9 * nominal;
                    totalKupon += kupon;
                    System.out.printf("%s - Rp %.2f - Kupon/bulan: Rp %.2f\n", nama, nominal, kupon);
                }
            }
        }

        System.out.printf("Total Kupon SBN/bulan: Rp %.2f\n", totalKupon);
    }
}

class Saham {
    String kode;
    String nama;
    double harga;

    Saham(String kode, String nama, double harga) {
        this.kode = kode;
        this.nama = nama;
        this.harga = harga;
    }
}

class SBN {
    String nama;
    double bunga;
    int jangkaWaktu;
    String tanggalJatuhTempo;
    double kuotaNasional;

    SBN(String nama, double bunga, int jangkaWaktu, String tanggalJatuhTempo, double kuotaNasional) {
        this.nama = nama;
        this.bunga = bunga;
        this.jangkaWaktu = jangkaWaktu;
        this.tanggalJatuhTempo = tanggalJatuhTempo;
        this.kuotaNasional = kuotaNasional;
    }
}
