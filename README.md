# Watsons Store Stock Checker

Bu proje, Watsons'ın online stoklarında olmayan ancak belirli bir şehirdeki (Örnek olarak İstanbul tercih edilmiştir.) mağazalarında mevcut olan ürünlerin linklerini döndüren bir Selenium tabanlı Python scriptidir.

## Kurulum

1. Bu projeyi klonlayın:
    ```sh
    git clone https://github.com/SalihaDugan/WatsonsStoreStockChecker.git
    cd WatsonsStoreStockChecker
    ```

2. Gerekli Python kütüphanelerini yükleyin:
    ```sh
    pip install selenium
    ```

3. Microsoft Edge WebDriver'ını (msedgedriver.exe) indirin ve çalıştırılabilir dosyanın yolunu ayarlayın.

## Kullanım

1. `droppedData` DataFrame'inizi hazırlayın. Bu DataFrame, ürünlerin linklerini içermelidir.

2. Aşağıdaki kodu çalıştırın:
    ```python
    import time
    from selenium import webdriver
    from selenium.webdriver.common.by import By
    from selenium.webdriver.support.ui import WebDriverWait
    from selenium.webdriver.support import expected_conditions as EC
    from selenium.common.exceptions import NoSuchElementException
    from selenium.webdriver.edge.service import Service

    stoktakiUrunler = []
    service = Service("msedgedriver.exe")
    driver = webdriver.Edge(service=service)

    i = 0
    while i < len(droppedData):
        sayfaLink = droppedData[0][i]
        driver.get(sayfaLink)
        time.sleep(2)

        if i == 0:
            driver.find_element(By.ID, "onetrust-accept-btn-handler").click()

        try:
            driver.find_element(By.CLASS_NAME, "rts-cta").click()
            wait = WebDriverWait(driver, 10)
            aramaKutusu = wait.until(EC.visibility_of_element_located((By.ID, "search")))
            aramaKutusu.send_keys("İstanbul")
            driver.find_element(By.CLASS_NAME, "store-locator-search-bar__submit").click()
            time.sleep(2)

            try:
                errorMessage = driver.find_element(By.CLASS_NAME, "store-locator-search-bar__error").text
            except NoSuchElementException:
                print(sayfaLink)
                stoktakiUrunler.append(sayfaLink)

            time.sleep(5)
        except NoSuchElementException:
            time.sleep(5)

        i += 1

    driver.quit()
    ```

3. `stoktakiUrunler` listesi, belirli şehirde mağazasında mevcut olan ürünlerin linklerini içerecektir.
