### 线程安全注解的互斥锁

#### 使用


```c++

class BankAccount {
private:
    Mutex mu;
    int balance GUARDED_BY(mu);

    void deposit_impl(int amount)
    {
        balance += amount;
    }

    void withdraw_impl(int amount) REQUIRES(mu)
    {
        balance -= amount;
    }

public:
    void withdraw(int amount)
    {
        withdraw_impl(amount);
    }

    void transfer_from(BankAccount& b, int amount)
    {
        MutexLockGuard lock(mu);
        b.withdraw(amount);
        deposit_impl(amount);
    }
};

```

```bash
> clang++ -std=c++11 -Wthread-safety

demo.cc:11:9: warning: writing variable 'balance' requires holding mutex 'mu' exclusively [-Wthread-safety-analysis]
        balance += amount;
        ^
demo.cc:22:9: warning: calling function 'withdraw_impl' requires holding mutex 'mu' exclusively [-Wthread-safety-analysis]
        withdraw_impl(amount);
        ^
2 warnings generated.
```
