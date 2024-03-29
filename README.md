# unchecked
A Rust library that you will never use it.

## Usage
```rust
struct A
{
    pub buf: [u8; 4096],
    pub num: i32,
}

impl A
{
    pub fn new() -> Self
    {
        return Self { buf: [0; 4096], num: 0, };
    }

    fn get_buf(&mut self) -> &mut [u8]
    {
        return &mut self.buf[..];
    }
}

// exclude: full pattern before indexing
// mut: name of method/field that you want to borrow mutable
#[unchecked(exclude = ["arr2", "a[0].buf"], mut = ["get_buf", "num"])]
fn main()
{
    let mut arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
    let arr1 = &mut arr[0..3];
    

    arr1[3] = arr1[4] + 1;
    arr1[3] += arr1[4] + 1;

    assert!(arr[3] == 12);  //can't convert inside marco, so use arr1[3] will panic

    let arr2 = &arr[0..3];

    let err = std::panic::catch_unwind(||
    {
        let _ = arr2[3];    //will panic
    });

    assert!(err.is_err());

    let mut a = [A::new()];
    a[0].get_buf()[10] = 100;    //force use get_unchecked_mut() to call get_buf()
    let num = a[0].get_buf()[10];
    a[0].num = 10;

    assert!(num == 100 && a[0].num == 10);

    let num = Some(2);
    let _ = num.unwrap(); // convert to unwrap_unchecked() if you enable the "unwrap" feature, can ignore through unwrap_exclude like exclude

    let _ = a[0].buf[0];  // buf[0] is excluded, but a[0] still
    println!("end");
}
```
P.S. The macro active only in release mode
