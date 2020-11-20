 #Read Me notes
 
 def original_click(self, by_type, value):
        ele = ''
        try:
            if by_type == 'id':
                ele = WebDriverWait(self.driver, 10).until(
                    EC.visibility_of_element_located((By.ID, value)))
            elif by_type == 'name':
                ele = WebDriverWait(self.driver, 10).until(
                    EC.visibility_of_element_located((By.NAME, value)))
            elif by_type == 'xpath':
                ele = WebDriverWait(self.driver, 10).until(
                    EC.visibility_of_element_located((By.XPATH, value)))
            elif by_type == 'cssSelector':
                ele = WebDriverWait(self.driver, 10).until(
                    EC.visibility_of_element_located((By.CSS_SELECTOR, value)))
            ele.click()
            log.info('通过' + str(by_type) + ' selenium原生方法显示等待定位并点击成功')
        except Exception as e:
            log.error('通过' + str(by_type) + '定位点击失败，请检查元素定位信息', e)
            self.get_screenshot_as_file()

    def original_sendkeys(self, by_type, element, content):
        if by_type == 'id':
            self.driver.find_element_by_id(element).send_keys(content)
        elif by_type == 'name':
            self.driver.find_element_by_name(element).send_keys(content)
        elif by_type == 'xpath':
            self.driver.find_element_by_xpath(element).send_keys(content)
        elif by_type == 'cssSelector':
            self.driver.find_element_by_css_selector(element).send_keys(content)
        log.info('通过定位' + str(by_type) + '输入【' + content + '】成功')

    def click_then_send_keys(self, element_beans, content):
        try:
            self.click(element_beans)
            self.send_keys_clear(element_beans, content)
            log.info("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】成功")
        except Exception as e:
            self.get_screenshot_as_file()
            log.error("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】失败", e)

    def send_keys(self, element_beans, content):
        try:
            self.click(element_beans)
            self.find_element(element_beans).send_keys(content)
            log.info("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】成功")
        except Exception as e:
            self.get_screenshot_as_file()
            log.error("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】失败", e)

    def send_keys_enter(self, element_beans, content):
        """
        输入内容后点击Enter键
        :param element_beans: 传入元素yaml文件定位的ele_name
        :param content: 需要输入的值
        :return:
        """
        try:
            # self.find_element(element_beans).clear()
            # log.info('清除【' + str(ele['desc']) + '】中的内容成功')
            # self.sleep(0.5)
            self.find_element(element_beans).send_keys(content)
            self.click_enter_button()
            log.info("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】后点击Enter成功")
        except Exception as e:
            self.get_screenshot_as_file()
            log.error("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】失败", e)

    def send_keys_clear(self, element_beans, content):
        """
        输入内容前先清空输入框内容
        :param element_beans: 传入元素yaml文件定位的ele_name
        :param content: 需要输入的值
        :return:
        """
        try:
            self.find_element(element_beans).clear()
            log.info('清除【' + str(element_beans.desc) + '】中的内容成功')
            self.sleep(0.5)
            self.find_element(element_beans).send_keys(content)
            log.info("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】成功")
        except Exception as e:
            self.get_screenshot_as_file()
            log.error("向【" + str(element_beans.desc) + "】输入【" + str(content) + "】失败", e)

    def send_keys_until_correct(self, element_beans, content):
        try:
            text = self.get_element_value_text(element_beans)
            self.sleep(1)
            while text != content:
                self.send_keys_clear(element_beans, content)
                text = self.get_element_value_text(element_beans)
                self.sleep(1)
            log.info('向【' + str(element_beans.desc) + '】输入【' + str(content) + '】直到成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('向【' + str(element_beans.desc) + '】输入【' + str(content) + '】失败', e)

    def send_keys_diff(self, element_beans):
        new_str = ''
        try:
            cur_str = self.get_element_value_text(element_beans)
            new_str = RandomUtil.random_string(length=10)
            while cur_str == new_str:
                new_str = RandomUtil.random_string(length=10)
            self.send_keys_clear(element_beans, new_str)
            log.info('向【' + str(element_beans.desc) + f'】输入{new_str}成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('向【' + str(element_beans.desc) + f'】输入{new_str}失败', e)

    def sleep(self, seconds):
        log.info("线程强制等待" + str(seconds) + "秒")
        time.sleep(seconds)

    def get_screenshot_as_file(self):
        ctime = time.strftime("%Y-%m-%d_%H.%M.%S")
        filename = f'{ctime}-failed.png'
        log.info(f'截图文件名称为：{filename}')
        try:
            root_path = os.path.dirname(os.path.dirname(__file__)).split(f'\\util')[0]
            # print('root_path: ----> ', root_path)
            path = self.config.get_value('config.conf', 'screenshot', 'screenshots_dir')
            # print('path: ----> ', path)
            screenshots_dir = os.path.join(root_path, path)
            # print('screenshots_dir: ----> ', screenshots_dir)
            log.info('截图文件的目录为：' + screenshots_dir)
            screenshot_filename = os.path.join(screenshots_dir, filename)
            # print(screenshot_filename)
            self.driver.get_screenshot_as_file(screenshot_filename)
            log.info(f'截图{ctime}' + '-failed.png生成成功')
        except Exception as e:
            log.error('截图失败，报错信息为：', e)

    def get_current_url(self):
        current_url = self.driver.current_url
        log.info('获取当前界面的url：【' + current_url + '】成功')
        return current_url

    def get_title(self):
        title = self.driver.title
        log.info('获取当前界面的title：【' + title + '】成功')
        return title

    def get_page_source(self):
        page_source = self.driver.page_source
        log.info('获取当前界面的page source成功')
        return page_source

    def get_element_attribute(self, element_beans, attr_name):
        """
        根据传入的属性获取元素属性值
        :param element_beans: 元素定位
        :param attr_name: 属性值
        :return:
        """
        attr = self.find_element(element_beans).get_attribute(attr_name)
        log.info('获取【' + str(element_beans.desc) + '】的元素属性【' + attr + '】成功')
        return attr

    def set_element_attribute(self, element_beans, attr_name, new_attr_value):
        context = self.find_element(element_beans)
        new_value = self.driver.execute_script(f"return arguments[0].{attr_name}='{new_attr_value}';", context)
        log.info('修改【' + str(element_beans.desc) + '】的元素属性：' + attr_name + '为' + str(new_attr_value) + '成功')
        return new_value

    def get_element_value_text(self, element_beans):
        """
        通过元素定位信息获取文本
        :param element_beans: 配置里配的元素定位
        :return:
        """
        text = self.find_element(element_beans).get_attribute('value')
        log.info('获取【' + str(element_beans.desc) + '】页面上的元素文本【' + str(text) + '】成功')
        return text

    def set_element_text(self, element_beans, new_value):
        element = self.find_element(element_beans)
        new_text = self.driver.execute_script(f"return arguments[0].value='{new_value}';", element)
        log.info('修改【' + str(element_beans.desc) + '】的元素属性Value值为：【' + str(new_value) + '】成功')
        return new_text

    def current_window_handle(self):
        cur_window_handle = self.driver.current_window_handle()
        log.info('获取当前界面的window handle成功：【' + cur_window_handle + '】')
        return cur_window_handle

    def window_handles(self):
        handles = self.driver.window_handles()
        log.info('获取当前界面的所有handles：【' + handles + '】成功')
        return handles

    def refresh(self):
        self.driver.refresh()
        log.info('刷新当前页面成功')

    def back(self):
        self.driver.back()
        log.info('浏览器上个页面返回成功')

    def forword(self):
        self.driver.forward()
        log.info('浏览器前进成功')

    def switch_to_alert(self):
        alert = None
        try:
            alert = self.driver.switch_to.alert
            log.info('alert切换成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('alert切换失败，报错信息为：', e)
        return alert

    def switch_to_frame(self, element_beans, index=None):
        try:
            self.driver.switch_to.frame(element_beans.value)
            log.info('切换frame成功')
        except:
            try:
                self.driver.switch_to.frame(self.find_element(element_beans))
            except:
                try:
                    self.driver.switch_to.frame(index)
                except Exception as e:
                    self.get_screenshot_as_file()
                    log.error('通过索引切换frame失败', e)
                self.get_screenshot_as_file()
                log.error('切换frame失败')

    def switch_to_parent_frame(self):
        self.driver.switch_to.parent_frame()
        log.info('切换到上级frame成功')

    def switch_to_default_frame(self):
        self.driver.switch_to.default_content()
        log.info('切换到最外层frame成功')

    def switch_to_window(self, index):
        handles = self.window_handles()
        self.driver.switch_to.window(handles[index])
        log.info('切换到第【' + str(index + 1) + '个handle成功')

    def execute_js(self, js):
        try:
            self.driver.execute_script(js)
            log.info('执行【' + js + 'js代码】成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('执行【' + js + 'js代码】失败', e)

    def set_calender_date(self, element_beans, new_date):
        """
        loc参数必须配置为id，目前只配置了id
        :param element_beans:
        :param new_date:  想要修改到的时间
        :return:
        """
        try:
            js = f"document.getElementById({element_beans.value}).value='{new_date}'"
            self.driver.execute_script(js)
            self.sleep(3)
            log.info('设置日历时间到【' + new_date + '】成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('设置日志时间到【' + new_date + '】失败', e)

    def scroll_to_top(self):
        js = "var q=document.documentElement.scrollTop=0"
        self.execute_js(js)
        log.info('滚动条滑动到最上边成功')

    def scroll_to_bottom(self):
        js = "var q=document.documentElement.scrollTop=10000"
        self.execute_js(js)
        log.info('滚动条滑动到最下边成功')

    def scroll_to_right(self):
        js = "var q=document.documentElement.scrollLeft=10000"
        self.execute_js(js)
        log.info('滚动条滑动到最右边成功')

    def scroll_to_element(self, element_beans):
        element = self.find_element(element_beans)
        self.driver.execute_script('arguments[0].scrollIntoView();', element)
        log.info('滚动到指定元素成功')

    def implicitly_wait(self, second):
        self.driver.implicitly_wait(second)
        log.info('设置全局等待【' + second + '】秒')

    def implicitly_wait_default(self):
        self.driver.implicitly_wait(self.config.get_value('config.conf', 'wait', 'implicitly_wait'))
        log.info('通过配置文件设置全局等待成功')

    def implicitly_wait_zero(self):
        self.driver.implicitly_wait(0)
        log.info('设置全局等待为0秒')

    def maximize_window(self):
        self.driver.maximize_window()
        log.info('最大化当前窗口成功')

    def add_cookie(self, cookie):
        self.driver.add_cookie(cookie)
        log.info('添加cookie【' + cookie + '】成功')

    def delete_all_cookies(self):
        self.driver.delete_all_cookies()
        log.info('删除所有cookie成功')

    def move_to_element(self, element_beans):
        try:
            element = self.find_element(element_beans)
            self.sleep(1)
            self.action.move_to_element(element)
            log.info('模拟鼠标悬浮到【' + str(element_beans.desc) + '】成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('模拟鼠标悬浮到【' + str(element_beans.desc) + '】失败', e)

    def click_table_button(self):
        self.sleep(1)
        self.action.key_down(Keys.TAB).perform()
        log.info('模拟键盘按下【Table键】成功')

    def click_enter_button(self):
        self.sleep(1)
        self.action.key_down(Keys.ENTER).perform()
        log.info('模拟键盘按下【Enter键】成功')

    def click_esc_button(self):
        self.sleep(1)
        self.action.key_down(Keys.ESCAPE).perform()
        log.info('模拟键盘按下【ESC键】成功')

    def click_backSpace_button(self):
        self.sleep(1)
        self.action.key_down(Keys.BACK_SPACE).perform()
        log.info('模拟键盘按下【BackSpace键】成功')

    def double_click(self, element_beans):
        element = self.find_element(element_beans)
        self.action.double_click(element).perform()
        log.info('模拟鼠标双击【' + str(element_beans.desc) + '】成功')

    def upload_file(self, element_beans, file_path, file_name):
        """
        遇到windows弹窗的文件上传，在多文件上传时，需单引号('')括起并且文件名与文件名之间需用空格隔开，
        再在最后一个文件名后添加一个空格；
        例如： '"小小.jpg" "小.jpg" '
        :param element_beans: 元素定位信息
        :param file_path: 文件存在的路径
        :param file_name: 文件名
        :return:
        """
        try:
            self.click(element_beans)
            app = Desktop()  # 创建操作桌面的对象
            dialog_window = app["打开"]  # 获取弹窗的窗口标题
            # dialog_window.print_ctrl_ids()   # 打印窗口的所有控件信息
            dialog_window["Toolbar3"].click()  # 获取文件路径填写输入框并点击
            self.sleep(1)
            send_keys(file_path)  # 在文件路径填写输入框输入文件存在的路径
            send_keys("{VK_RETURN}")  # 输入文件路径后按回车键
            self.sleep(1)
            dialog_window["文件名(&N):Edit"].type_keys(file_name)  # 获取文件名输入框并填写文件名
            self.sleep(1)
            dialog_window["打开(&O)"].click()  # 获取'打开'控件并点击
            log.info(f'文件{file_name}上传成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error(f'文件{file_name}上传失败', e)

    def select_radio(self, element_beans):
        self.click(element_beans)
        log.info('点击【radio按钮】成功')

    def select_radio_diff(self, element_beans):
        eles = self.find_elements(element_beans)
        for i in eles:
            if not i.is_selected():
                i.click()
        log.info('点击【不相同的radio按钮】成功')

    def select_option_by_index(self, element_beans, index):
        """
        通过定位一组列表元素，通过下标选择option，yaml文件里配合一组元素中的一个即可
        :param element_beans: yaml文件里配的一组元素的定位信息的其中一个
        :param index: option在列表里的下标, 下标从0开始
        :return:
        """
        try:
            eles = self.find_elements(element_beans)
            log.info('通过find_elements共定位到【' + str(len(eles)) + '】组元素')
            self.sleep(2.5)
            eles[index].click()
            log.info('通过下标选择第【' + str(index + 1) + '】个成功】')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('通过下标选择第【' + str(index + 1) + '】个成功】，报错信息为：', e)

    def select_option_by_text(self, element_beans, text):
        """
        通过定位一组列表元素，通过文本选择option，yaml文件里配合一组元素中的一个即可
        :param element_beans: yaml文件里配的一组元素的定位信息的其中一个
        :param text: option等于列表里文本
        :return:
        """
        try:
            eles = self.find_elements(element_beans)
            log.info('通过find_elements共定位到【' + str(len(eles)) + '】组元素')
            for i in eles:
                if i.text == text:
                    self.sleep(2.5)
                    i.click()
            log.info('通过text选择【' + str(text) + '】选择成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('【通过text选择【' + str(text) + '】选择失败，报错信息为：', e)

    def select_option_by_part_text(self, element_beans, text):
        """
        通过定位一组列表元素，通过部分文本选择option，yaml文件里配合一组元素中的一个即可
        :param element_beans: yaml文件里配的一组元素的定位信息的其中一个
        :param text: option在列表里文本
        :return:
        """
        try:
            eles = self.find_elements(element_beans)
            log.info('通过find_elements共定位到【' + str(len(eles)) + '】组元素')
            for i in eles:
                if text in i.text:
                    self.sleep(2.5)
                    i.click()
                    break
            log.info('通过text选择【' + str(text) + '】选择成功')
        except Exception as e:
            self.get_screenshot_as_file()
            log.error('【通过text选择【' + str(text) + '】选择失败，报错信息为：', e)

    def is_element_exist(self, element_beans):
        self.implicitly_wait_zero()
        flag = False
        try:
            flag = self.find_element(element_beans).is_displayed()
            log.error('元素【' + str(element_beans.desc) + '】存在')
            return flag
        except Exception as e:
            log.error('元素【' + str(element_beans.desc) + element_beans.timeout + '】内未找到', e)
        finally:
            self.implicitly_wait_default()
        return flag
