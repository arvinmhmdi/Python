import json
import getpass
import os

USERS_FILE = "users.json"

# بارگذاری اطلاعات کاربران از فایل
def load_users():
    if not os.path.exists(USERS_FILE):
        return {}
    with open(USERS_FILE, "r") as file:
        return json.load(file)

# ذخیره اطلاعات در فایل
def save_users(users):
    with open(USERS_FILE, "w") as file:
        json.dump(users, file, indent=4)

# تابع ورود یا ثبت نام
def login(users):
    username = input("نام کاربری: ")
    password = getpass.getpass("رمز عبور: ")

    if username in users:
        if users[username]["password"] == password:
            print("ورود موفقیت‌آمیز بود!")
            return username
        else:
            print("رمز اشتباه است.")
            return None
    else:
        print("کاربر جدید شناسایی شد. ثبت‌نام در حال انجام...")
        users[username] = {
            "password": password,
            "balance": 0
        }
        save_users(users)
        print("ثبت‌نام با موفقیت انجام شد!")
        return username

# نمایش منو
def show_menu():
    print("\n--- منوی ATM ---")
    print("1. بررسی موجودی")
    print("2. واریز پول")
    print("3. برداشت پول")
    print("4. خروج")

# برنامه اصلی
def main():
    users = load_users()
    username = None

    while username is None:
        username = login(users)

    while True:
        show_menu()
        choice = input("انتخاب کنید (1-4): ")

        if choice == '1':
            print(f"موجودی فعلی: {users[username]['balance']} تومان")

        elif choice == '2':
            try:
                amount = int(input("مبلغ واریزی را وارد کنید: "))
                if amount > 0:
                    users[username]['balance'] += amount
                    save_users(users)
                    print(f"{amount} تومان با موفقیت واریز شد.")
                else:
                    print("مبلغ نامعتبر است.")
            except ValueError:
                print("لطفاً فقط عدد وارد کنید.")

        elif choice == '3':
            try:
                amount = int(input("مبلغ برداشتی را وارد کنید: "))
                if 0 < amount <= users[username]['balance']:
                    users[username]['balance'] -= amount
                    save_users(users)
                    print(f"{amount} تومان برداشت شد.")
                else:
                    print("موجودی کافی نیست یا مبلغ نامعتبر است.")
            except ValueError:
                print("لطفاً فقط عدد وارد کنید.")

        elif choice == '4':
            print("خروج از حساب. موفق باشید!")
            break
        else:
            print("انتخاب نامعتبر. لطفاً عدد 1 تا 4 را وارد کنید.")

if __name__ == "__main__":
    main()

