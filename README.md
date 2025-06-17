# KULON--MARKET
import { useState } from "react";

interface Produk {
  id: number;
  nama: string;
  harga: number;
  satuan: string;
}

interface ItemKeranjang extends Produk {
  jumlah: number;
}

const produkList: Produk[] = [
  { id: 1, nama: "Bayam", harga: 5000, satuan: "ikat" },
  { id: 2, nama: "Tomat", harga: 8000, satuan: "kg" },
  { id: 3, nama: "Wortel", harga: 10000, satuan: "kg" },
  { id: 4, nama: "Sawi", harga: 6000, satuan: "ikat" },
];

export default function App() {
  const [keranjang, setKeranjang] = useState<ItemKeranjang[]>([]);
  const [pesan, setPesan] = useState("");

  const tambah = (produk: Produk) => {
    setKeranjang((prev) => {
      const ada = prev.find((item) => item.id === produk.id);
      if (ada) {
        return prev.map((item) =>
          item.id === produk.id ? { ...item, jumlah: item.jumlah + 1 } : item
        );
      }
      return [...prev, { ...produk, jumlah: 1 }];
    });
    setPesan(`${produk.nama} ditambahkan ke keranjang`);
    setTimeout(() => setPesan(""), 1500);
  };

  const kurang = (produk: Produk) => {
    setKeranjang((prev) =>
      prev
        .map((item) =>
          item.id === produk.id ? { ...item, jumlah: item.jumlah - 1 } : item
        )
        .filter((item) => item.jumlah > 0)
    );
  };

  const totalHarga = keranjang.reduce(
    (total, item) => total + item.harga * item.jumlah,
    0
  );

  const checkout = () => {
    const isiPesan = keranjang
      .map(
        (item) =>
          `${item.nama} (${item.jumlah} ${item.satuan}) - Rp${item.harga * item.jumlah}`
      )
      .join("%0A");
    const link = `https://wa.me/62xxxxxxxxxx?text=Pesan:%0A${isiPesan}%0ATotal:%20Rp${totalHarga}`;
    window.open(link, "_blank");
  };

  return (
    <div style={{ maxWidth: 400, margin: "0 auto", padding: 20, fontFamily: "sans-serif" }}>
      <h1 style={{ textAlign: "center" }}>Kulon Sayur</h1>

      {pesan && (
        <div
          style={{
            backgroundColor: "#d4edda",
            color: "#155724",
            padding: "5px 10px",
            borderRadius: 5,
            marginBottom: 10,
            textAlign: "center",
            fontSize: 14,
          }}
        >
          {pesan}
        </div>
      )}

      {produkList.map((produk) => (
        <div
          key={produk.id}
          style={{
            border: "1px solid #ccc",
            borderRadius: 5,
            marginBottom: 10,
            padding: 10,
          }}
        >
          <div>
            <strong>{produk.nama}</strong> <br />
            <small>Rp{produk.harga} / {produk.satuan}</small>
          </div>
          <button
            onClick={() => tambah(produk)}
            style={{
              marginTop: 5,
              backgroundColor: "#28a745",
              color: "#fff",
              border: "none",
              padding: "5px 10px",
              borderRadius: 3,
              cursor: "pointer",
            }}
          >
            + Tambah
          </button>
        </div>
      ))}

      <hr />
      <h2>Keranjang:</h2>
      {keranjang.length === 0 && <p>Belum ada produk</p>}

      <ul style={{ listStyle: "none", padding: 0 }}>
        {keranjang.map((item) => (
          <li
            key={item.id}
            style={{
              marginBottom: 8,
              display: "flex",
              justifyContent: "space-between",
              alignItems: "center",
            }}
          >
            <span>
              {item.nama} ({item.jumlah} {item.satuan})
              <br />
              <small>Rp{item.harga * item.jumlah}</small>
            </span>
            <div>
              <button
                onClick={() => kurang(item)}
                style={{
                  padding: "2px 6px",
                  marginRight: 4,
                  backgroundColor: "#dc3545",
                  color: "#fff",
                  border: "none",
                  borderRadius: 3,
                  cursor: "pointer",
                }}
              >
                −
              </button>
              <button
                onClick={() => tambah(item)}
                style={{
                  padding: "2px 6px",
                  backgroundColor: "#28a745",
                  color: "#fff",
                  border: "none",
                  borderRadius: 3,
                  cursor: "pointer",
                }}
              >
                +
              </button>
            </div>
          </li>
        ))}
      </ul>

      <p><strong>Total: Rp{totalHarga}</strong></p>

      <button
        onClick={checkout}
        disabled={keranjang.length === 0}
        style={{
          backgroundColor: keranjang.length === 0 ? "#ccc" : "#007bff",
          color: "#fff",
          border: "none",
          padding: "8px 15px",
          borderRadius: 5,
          cursor: keranjang.length === 0 ? "not-allowed" : "pointer",
          width: "100%",
        }}
      >
        Checkout via WhatsApp
      </button>
    </div>
  );
}
