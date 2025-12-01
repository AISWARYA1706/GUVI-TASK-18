login_page.py
from selenium.webdriver.common.by import By

class LoginPage:

    def _init_(self, driver):
        self.driver = driver

    username_box = (By.ID, "username")
    password_box = (By.ID, "password")
    login_btn = (By.ID, "loginBtn")
    logout_btn = (By.ID, "logoutBtn")
    error_msg = (By.ID, "error")

    def enter_username(self, username):
        self.driver.find_element(*self.username_box).send_keys(username)

    def enter_password(self, password):
        self.driver.find_element(*self.password_box).send_keys(password)

    def click_login(self):
        self.driver.find_element(*self.login_btn).click()

    def click_logout(self):
        self.driver.find_element(*self.logout_btn).click()

    def is_input_visible(self):
        return (self.driver.find_element(*self.username_box).is_displayed() and
                self.driver.find_element(*self.password_box).is_displayed())

    def is_login_button_enabled(self):
        return self.driver.find_element(*self.login_btn).is_enabled()

    def is_error_displayed(self):
        return self.driver.find_element(*self.error_msg).is_displayed()

login_step.py
@given("User is on Zen Portal Login Page")
def step_open_login_page(context):
    context.driver = webdriver.Chrome()
    context.driver.get("https://zenportal/login")
    context.driver.maximize_window()
    context.page = LoginPage(context.driver)

@when("User enters valid username and password")
def step_valid_credentials(context):
    context.page.enter_username("validUser")
    context.page.enter_password("validPass")

@when("User enters invalid username and password")
def step_invalid_credentials(context):
    context.page.enter_username("wrong")
    context.page.enter_password("wrong")

@when("User clicks login button")
def step_click_login(context):
    context.page.click_login()

@then("User should be logged in successfully")
def step_login_success(context):
    allure.attach(context.driver.get_screenshot_as_png(), name="Success Login",
                  attachment_type=allure.attachment_type.PNG)
    assert "dashboard" in context.driver.current_url

@then("Error message should be displayed")
def step_error_msg(context):
    assert context.page.is_error_displayed()

@then("Username and Password input boxes should be visible")
def step_validate_inputs(context):
    assert context.page.is_input_visible()

@then("Login button should be enabled")
def step_validate_button(context):
    assert context.page.is_login_button_enabled()

@given("User is logged in to Zen Portal")
def step_logged_in(context):
    context.driver = webdriver.Chrome()
    context.driver.get("https://zenportal/login")
    context.page = LoginPage(context.driver)
    context.page.enter_username("validUser")
    context.page.enter_password("validPass")
    context.page.click_login()

@when("User clicks on logout button")
def step_logout(context):
    context.page.click_logout()

@then("User should be logged out successfully")
def step_logged_out(context):
    allure.attach(context.driver.get_screenshot_as_png(), name="Logout",
                  attachment_type=allure.attachment_type.PNG)
    assert "login" in context.driver.current_url


environment.py
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from webdriver_manager.chrome import ChromeDriverManager

def before_scenario(context, scenario):
    pass

def after_scenario(context, scenario):
    if context.driver:
        context.driver.quit()

