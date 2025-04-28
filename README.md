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


public class investasiapp {
    static Scanner scanner = new Scanner(System.in);
    static Map<String, user> users = new HashMap<>();
    static List<Saham> sahamList = new ArrayList<>();
    static List<SBN> sbnList = new ArrayList<>();
kode program tersebut merupakan kelas publik yang mendeklarasikan dan membuat map untuk menyimpan imputan dari user dan disimpan serta membuat list-list sahan dan SBN yang ada.

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
kode program tersebut  merupakan method berfungsi untuk mengisi data awal dan menampilkan menu utama yang ada didalam kode program dengan berulang-ulang. Jika user memilih 1 maka akan login, namun jika user memilih 0 maka loop akan berhenti karna user memilih keluar program.

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
kode program method berikut adalah data-data yang sudah kami set di dalam seedData(). Terdapat username untuk admin dan customer dan daftar-daftar pilihan saham yang tersedia dan SBN yang tersedia.


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

Method login() bertugas menangani proses login pengguna dengan meminta input username dan password melalui scanner. Setelah menerima input, program mencari objek user dari Map users berdasarkan username yang dimasukkan. Jika objek user ditemukan (user != null) dan password yang dimasukkan cocok dengan password user tersebut (diperiksa lewat method checkPassword(password)), maka sistem memanggil method menu() milik user tersebut, dengan mengoper daftar saham (sahamList) dan daftar SBN (sbnList) sebagai parameter. Jika username tidak ditemukan atau password salah, maka program akan mencetak pesan "Login gagal!" ke layar.

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

Kelas user adalah kelas abstrak yang mendefinisikan atribut dasar username dan passwor`, menyediakan konstruktor untuk menginisialisasi keduanya, serta method checkPassword() untuk memverifikasi kecocokan password, dan method abstrak menu() yang harus diimplementasikan oleh subclass. Kelas admin adalah turunan dari user yang mengimplementasikan method menu() khusus untuk admin, di mana admin dapat memilih beberapa operasi melalui menu berulang: menambahkan saham baru, mengubah harga saham yang sudah ada, menambahkan produk SBN baru, atau logout. Setiap pilihan menu meminta input dari admin menggunakan scanner, lalu memanipulasi daftar sahamList dan sbnList  sesuai dengan perintah yang dipilih, sedangkan opsi logout akan mengakhiri menu dengan return.

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

Kelas Customer adalah turunan dari kelas user yang mewakili pengguna biasa (bukan admin) dengan tambahan dua atribut berupa Map untuk mencatat saham (sahamOwned) dan SBN (sbnOwned) yang dimiliki. Konstruktor Customermenginisialisasi username dan password dengan memanggil konstruktor super. Method menu() diimplementasikan untuk menampilkan pilihan menu interaktif, memungkinkan customer melakukan berbagai tindakan seperti membeli saham, menjual saham, membeli SBN, melakukan simulasi kupon SBN, dan melihat portofolio investasi mereka. Setiap pilihan akan memanggil method terkait seperti beliSaham(), jualSaham(), beliSBN(), simulasiSBN(), atau lihatPortofolio(), dan opsi logout (6) akan mengembalikan pengguna ke menu utama dengan return.

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

Method beliSaham() digunakan untuk menampilkan daftar semua saham yang tersedia dari sahamList, meminta pengguna memilih saham berdasarkan nomor, lalu meminta jumlah lembar saham yang ingin dibeli; saham yang dibeli akan disimpan ke dalam sahamOwned, dengan memperbarui jumlah lembar jika sudah ada. Sedangkan jualSaham()digunakan untuk menampilkan daftar saham yang dimiliki pengguna, meminta pengguna memilih saham yang ingin dijual, lalu menentukan jumlah lembar yang dijual; program akan mengecek apakah jumlah yang dijual tidak melebihi kepemilikan, dan jika jumlah saham untuk suatu kode menjadi nol setelah dijual, data tersebut akan dihapus dari sahamOwned. Kedua method ini menggunakan input dari pengguna dan memberikan feedback sukses atau gagal berdasarkan validasi yang dilakukan.

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

Method beliSBN() digunakan untuk menampilkan daftar seluruh produk SBN (sbnList) beserta detail bunga dan kuota nasional yang tersedia, kemudian meminta pengguna memilih SBN berdasarkan nomor dan menentukan nominal pembelian; jika nominal yang diminta melebihi kuota nasional, pembelian akan ditolak, tetapi jika cukup, nominal tersebut akan ditambahkan ke sbnOwned milik pengguna dan kuota nasional SBN akan dikurangi. Sementara itu, method simulasiSBN() digunakan untuk menghitung dan menampilkan simulasi kupon (pendapatan bunga) per bulan dari semua SBN yang dimiliki pengguna, dengan perhitungan berdasarkan bunga tahunan yang dibagi 12, dikalikan 90% (mengasumsikan pajak 10%), dan dikalikan nominal yang dimiliki di masing-masing SBN.

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

Method lihatPortofolio() digunakan untuk menampilkan seluruh aset investasi yang dimiliki customer, baik dari saham maupun SBN. Pertama, program mencetak daftar saham yang dimiliki (sahamOwned) dengan menghitung nilai total masing-masing saham (jumlah lembar dikalikan harga per lembar yang diambil dari sahamList) dan menjumlahkan seluruh nilai tersebut ke totalSaham. Setelah itu, program mencetak total investasi saham yang dimiliki. Selanjutnya, untuk bagian SBN (sbnOwned), program menampilkan daftar SBN yang dimiliki beserta nominal investasinya dan menghitung estimasi kupon bulanan berdasarkan bunga tahunan (dibagi 12 bulan dan dikalikan 90% untuk memperhitungkan pajak), lalu semua kupon tersebut dijumlahkan ke totalKupon. Terakhir, program menampilkan total pendapatan kupon bulanan dari seluruh SBN yang dimiliki.

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

Kelas Saham adalah kelas sederhana yang merepresentasikan data sebuah saham dengan tiga atribut utama: kode (kode unik saham), nama (nama perusahaan penerbit saham), dan harga (harga per lembar saham). Konstruktor Sahamdigunakan untuk menginisialisasi ketiga atribut ini ketika sebuah objek saham baru dibuat. Kelas ini berfungsi sebagai struktur data untuk menyimpan dan mengelola informasi saham yang nantinya dapat ditampilkan, dibeli, atau dijual oleh pengguna dalam aplikasi investasi.

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

Kelas SBN adalah kelas yang digunakan untuk merepresentasikan data mengenai Surat Berharga Negara (SBN), dengan atribut utama berupa nama (nama SBN), bunga (persentase bunga tahunan yang ditawarkan oleh SBN), jangkaWaktu(durasi investasi dalam bulan), tanggalJatuhTempo (tanggal jatuh tempo atau tanggal berakhirnya investasi), dan kuotaNasional (jumlah kuota SBN yang masih tersedia untuk dibeli). Konstruktor SBN digunakan untuk menginisialisasi atribut-atribut tersebut ketika objek SBN baru dibuat. Kelas ini berguna untuk mengelola data terkait SBN yang dapat dibeli oleh pengguna dalam aplikasi investasi, serta menyediakan informasi penting seperti bunga dan kuota yang tersedia.


public class investasiapp {
    static Scanner scanner = new Scanner(System.in);
    static Map<String, user> users = new HashMap<>();
    static List<Saham> sahamList = new ArrayList<>();
    static List<SBN> sbnList = new ArrayList<>();
kode program tersebut merupakan kelas publik yang mendeklarasikan dan membuat map untuk menyimpan imputan dari user dan disimpan serta membuat list-list sahan dan SBN yang ada.

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
kode program tersebut  merupakan method berfungsi untuk mengisi data awal dan menampilkan menu utama yang ada didalam kode program dengan berulang-ulang. Jika user memilih 1 maka akan login, namun jika user memilih 0 maka loop akan berhenti karna user memilih keluar program.

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
kode program method berikut adalah data-data yang sudah kami set di dalam seedData(). Terdapat username untuk admin dan customer dan daftar-daftar pilihan saham yang tersedia dan SBN yang tersedia.


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

Method login() bertugas menangani proses login pengguna dengan meminta input username dan password melalui scanner. Setelah menerima input, program mencari objek user dari Map users berdasarkan username yang dimasukkan. Jika objek user ditemukan (user != null) dan password yang dimasukkan cocok dengan password user tersebut (diperiksa lewat method checkPassword(password)), maka sistem memanggil method menu() milik user tersebut, dengan mengoper daftar saham (sahamList) dan daftar SBN (sbnList) sebagai parameter. Jika username tidak ditemukan atau password salah, maka program akan mencetak pesan "Login gagal!" ke layar.

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

Kelas user adalah kelas abstrak yang mendefinisikan atribut dasar username dan passwor`, menyediakan konstruktor untuk menginisialisasi keduanya, serta method checkPassword() untuk memverifikasi kecocokan password, dan method abstrak menu() yang harus diimplementasikan oleh subclass. Kelas admin adalah turunan dari user yang mengimplementasikan method menu() khusus untuk admin, di mana admin dapat memilih beberapa operasi melalui menu berulang: menambahkan saham baru, mengubah harga saham yang sudah ada, menambahkan produk SBN baru, atau logout. Setiap pilihan menu meminta input dari admin menggunakan scanner, lalu memanipulasi daftar sahamList dan sbnList  sesuai dengan perintah yang dipilih, sedangkan opsi logout akan mengakhiri menu dengan return.

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

Kelas Customer adalah turunan dari kelas user yang mewakili pengguna biasa (bukan admin) dengan tambahan dua atribut berupa Map untuk mencatat saham (sahamOwned) dan SBN (sbnOwned) yang dimiliki. Konstruktor Customermenginisialisasi username dan password dengan memanggil konstruktor super. Method menu() diimplementasikan untuk menampilkan pilihan menu interaktif, memungkinkan customer melakukan berbagai tindakan seperti membeli saham, menjual saham, membeli SBN, melakukan simulasi kupon SBN, dan melihat portofolio investasi mereka. Setiap pilihan akan memanggil method terkait seperti beliSaham(), jualSaham(), beliSBN(), simulasiSBN(), atau lihatPortofolio(), dan opsi logout (6) akan mengembalikan pengguna ke menu utama dengan return.

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

Method beliSaham() digunakan untuk menampilkan daftar semua saham yang tersedia dari sahamList, meminta pengguna memilih saham berdasarkan nomor, lalu meminta jumlah lembar saham yang ingin dibeli; saham yang dibeli akan disimpan ke dalam sahamOwned, dengan memperbarui jumlah lembar jika sudah ada. Sedangkan jualSaham()digunakan untuk menampilkan daftar saham yang dimiliki pengguna, meminta pengguna memilih saham yang ingin dijual, lalu menentukan jumlah lembar yang dijual; program akan mengecek apakah jumlah yang dijual tidak melebihi kepemilikan, dan jika jumlah saham untuk suatu kode menjadi nol setelah dijual, data tersebut akan dihapus dari sahamOwned. Kedua method ini menggunakan input dari pengguna dan memberikan feedback sukses atau gagal berdasarkan validasi yang dilakukan.

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

Method beliSBN() digunakan untuk menampilkan daftar seluruh produk SBN (sbnList) beserta detail bunga dan kuota nasional yang tersedia, kemudian meminta pengguna memilih SBN berdasarkan nomor dan menentukan nominal pembelian; jika nominal yang diminta melebihi kuota nasional, pembelian akan ditolak, tetapi jika cukup, nominal tersebut akan ditambahkan ke sbnOwned milik pengguna dan kuota nasional SBN akan dikurangi. Sementara itu, method simulasiSBN() digunakan untuk menghitung dan menampilkan simulasi kupon (pendapatan bunga) per bulan dari semua SBN yang dimiliki pengguna, dengan perhitungan berdasarkan bunga tahunan yang dibagi 12, dikalikan 90% (mengasumsikan pajak 10%), dan dikalikan nominal yang dimiliki di masing-masing SBN.

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

Method lihatPortofolio() digunakan untuk menampilkan seluruh aset investasi yang dimiliki customer, baik dari saham maupun SBN. Pertama, program mencetak daftar saham yang dimiliki (sahamOwned) dengan menghitung nilai total masing-masing saham (jumlah lembar dikalikan harga per lembar yang diambil dari sahamList) dan menjumlahkan seluruh nilai tersebut ke totalSaham. Setelah itu, program mencetak total investasi saham yang dimiliki. Selanjutnya, untuk bagian SBN (sbnOwned), program menampilkan daftar SBN yang dimiliki beserta nominal investasinya dan menghitung estimasi kupon bulanan berdasarkan bunga tahunan (dibagi 12 bulan dan dikalikan 90% untuk memperhitungkan pajak), lalu semua kupon tersebut dijumlahkan ke totalKupon. Terakhir, program menampilkan total pendapatan kupon bulanan dari seluruh SBN yang dimiliki.

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

Kelas Saham adalah kelas sederhana yang merepresentasikan data sebuah saham dengan tiga atribut utama: kode (kode unik saham), nama (nama perusahaan penerbit saham), dan harga (harga per lembar saham). Konstruktor Sahamdigunakan untuk menginisialisasi ketiga atribut ini ketika sebuah objek saham baru dibuat. Kelas ini berfungsi sebagai struktur data untuk menyimpan dan mengelola informasi saham yang nantinya dapat ditampilkan, dibeli, atau dijual oleh pengguna dalam aplikasi investasi.

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

Kelas SBN adalah kelas yang digunakan untuk merepresentasikan data mengenai Surat Berharga Negara (SBN), dengan atribut utama berupa nama (nama SBN), bunga (persentase bunga tahunan yang ditawarkan oleh SBN), jangkaWaktu(durasi investasi dalam bulan), tanggalJatuhTempo (tanggal jatuh tempo atau tanggal berakhirnya investasi), dan kuotaNasional (jumlah kuota SBN yang masih tersedia untuk dibeli). Konstruktor SBN digunakan untuk menginisialisasi atribut-atribut tersebut ketika objek SBN baru dibuat. Kelas ini berguna untuk mengelola data terkait SBN yang dapat dibeli oleh pengguna dalam aplikasi investasi, serta menyediakan informasi penting seperti bunga dan kuota yang tersedia.




