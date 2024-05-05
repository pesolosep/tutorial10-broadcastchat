# tutorial10-broadcastchat

![alt text](img/2.1.png)

Untuk menjalankan program broadcast chat kita perlu eksekusi server dengan `cargo run --bin server` dan dengan konteks tutorial jalankan 3 klien dengan menggunakan perintah `cargo run --bin client` pada terminal terpisah, tiga client tersebut terhubung pada server yang sama dengan port yang berbeda, saat teks diberikan melalui suatu client, maka server akan menerima teks tersebut dan melakukan broadcast ke seluruh client yang terhubung dengan server.

## before changing client port (client change only)
![alt text](<img/2.2 before changing client port.png>)

## after changing both ports
![alt text](<img/2.2 after changing server and client port.png>)

Saat memodifikasi port pada sisi client, maka pada program server juga perlu dilakukan modifikasi portnya menjadi 8080 pada bagian TCP Listener, hal ini membuat server dan client dapat terhubung dengan koneksi websocket yang sama dengan port yang berbeda dari sebelumnya.


![alt text](<img/2.3 smallchange.png>)

Dari `client.rs`, kita ketahui bahwa dia menerima broadcast dari `server.rs`
```rust
incoming = ws_stream.next() => {
    match incoming {
        Some(Ok(msg)) => {
            if let Some(text) = msg.as_text() {
                println!("Pesol's Computer - From Server: {}", text);
            }
        },
        Some(Err(err)) => return Err(err.into()),
        None => return Ok(()),
    }
}
```
Sehingga kita perlu konversikan text yang dikirim dari `server.rs` untuk mengirim IP nya juga.
``` rust
bcast_tx.send(text.into())?;
```

diubah menjadi

```rust
bcast_tx.send(format!("{:?}: {}", addr, text))?;
```

Sehingga dengan ini messsage yang di broadcast tidak hanya text message, tetapi address dari client yang mengirimkan teks tersebut akan ikut di broadcast juga.