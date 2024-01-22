# Reqwest

## SSL证书

忽略 *SSL* 证书


```
  use reqwest::{Client, Result};

#[tokio::main]
async fn main() -> Result<()> {
  let clinet = Client::builder().danger_accept_invalid_certs(true)
    .build()
    .unwrap();
  let url = "https://example.com";
  let result = client
    .get(url)
    .send()
    .await?
    .text()
    .await?;
    println!("result is {:?}",result);
    Ok(())
}
```

