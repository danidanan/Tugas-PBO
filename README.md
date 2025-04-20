import java.util.*;

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
