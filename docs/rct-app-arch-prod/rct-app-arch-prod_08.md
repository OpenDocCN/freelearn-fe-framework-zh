

# 第八章：测试

我们终于完成了应用程序的开发。在我们将其发布到生产之前，我们想确保一切按预期工作。

在本章中，我们将学习如何使用不同的测试方法来测试我们的应用程序。这将给我们信心重构应用程序，构建新功能，修改现有功能，而不用担心破坏当前应用程序的行为。

我们将涵盖以下主题：

+   单元测试

+   集成测试

+   端到端测试

到本章结束时，我们将知道如何使用不同的方法和工具来测试我们的应用程序。

# 技术要求

在我们开始之前，我们需要设置我们的项目。为了能够开发我们的项目，我们需要在计算机上安装以下内容：

+   **Node.js** 版本 16 或更高版本以及 **npm** 版本 8 或更高版本

安装 Node.js 和 npm 有多种方法。这里有一篇很好的文章，详细介绍了更多细节：[`www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js`](https://www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js)。

+   **VSCode**（可选）目前是 JavaScript/TypeScript 最受欢迎的编辑器/IDE，因此我们将使用它。它是开源的，与 TypeScript 集成良好，我们可以通过扩展来扩展其功能。可以从 [`code.visualstudio.com/`](https://code.visualstudio.com/) 下载。

本章的代码文件可以在以下位置找到：[`github.com/PacktPublishing/React-Application-Architecture-for-Production`](https://github.com/PacktPublishing/React-Application-Architecture-for-Production)。

可以使用以下命令在本地克隆存储库：

```js
git clone https://github.com/PacktPublishing/React-Application-Architecture-for-Production.git
```

一旦克隆了存储库，我们需要安装应用程序的依赖项：

```js
npm install
```

我们可以使用以下命令提供环境变量：

```js
cp .env.example .env
```

一旦安装了依赖项，我们需要选择与本章匹配的正确代码库阶段。我们可以通过执行以下命令来完成：

```js
npm run stage:switch
```

此命令将为我们提供每个章节的阶段列表：

```js
? What stage do you want to switch to? (Use arrow
 keys)
❯ chapter-02
  chapter-03
  chapter-03-start
  chapter-04
  chapter-04-start
  chapter-05
  chapter-05-start
(Move up and down to reveal more choices)
```

这是第八章，因此如果我们想跟随，可以选择 `chapter-08-start`，或者选择 `chapter-08` 来查看本章的最终结果。

一旦选择了章节，所有跟随该章节所需的文件将显示出来。

关于设置细节的更多信息，请查看 `README.md` 文件。

# 单元测试

单元测试是一种测试方法，其中应用程序单元在隔离状态下进行测试，不依赖于其他部分。

对于单元测试，我们将使用 Jest，这是测试 JavaScript 应用程序最流行的框架。

在我们的应用程序中，我们将对通知存储进行单元测试。

让我们打开 `src/stores/notifications/__tests__/notifications.test.ts` 文件并添加以下内容：

```js
import {
  notificationsStore,
  Notification,
} from '../notifications';
const notification = {
  id: '123',
  title: 'Hello World',
  type: 'info',
  message: 'This is a notification',
} as Notification;
describe('notifications store', () => {
  it('should show and dismiss notifications', () => {
    // 1
    expect(
      notificationsStore.getState().notifications.length
    ).toBe(0);
    // 2
    notificationsStore
      .getState()
      .showNotification(notification);
    expect(
      notificationsStore.getState().notifications
    ).toContainEqual(notification);
    // 3
    notificationsStore
      .getState()
      .dismissNotification(notification.id);
    expect(
      notificationsStore.getState().notifications
    ).not.toContainEqual(notification);
  });
});
```

通知测试工作如下：

1.  我们断言 `notifications` 数组最初是空的。

1.  然后，我们触发`showNotification`动作，并测试新创建的通知是否存在于`notifications`数组中。

1.  最后，我们调用`dismissNotification`函数来取消通知，并确保通知已从`notifications`数组中移除。

要运行单元测试，我们可以执行以下命令：

```js
npm run test
```

单元测试的另一个用例将是各种实用函数和可重用组件，包括可以单独测试的逻辑。然而，在我们的案例中，我们将主要使用集成测试来测试我们的组件，这将在下一节中看到。

# 集成测试

集成测试是一种测试方法，其中测试应用程序的多个部分。集成测试通常比单元测试更有帮助，并且大多数应用程序测试应该是集成测试。

集成测试更有价值，因为它们可以增加我们对应用程序的信心，因为我们正在测试不同部分的功能、它们之间的关系以及它们如何进行通信。

对于集成测试，我们将使用 Jest 和 React Testing Library。这是一种测试应用程序功能的好方法，就像用户使用它一样。

在`src/testing/test-utils.ts`中，我们可以定义一些我们可以在测试中使用的实用工具。我们还应该从这里重新导出 React Testing Library 提供的所有实用工具，这样我们就可以在测试中需要时轻松访问它们。目前，除了 React Testing Library 提供的所有函数外，我们还导出以下实用工具：

+   `appRender`是一个函数，它调用 React Testing Library 中的`render`函数，并将`AppProvider`作为`wrapper`。我们需要这样做，因为在我们进行集成测试时，我们的组件依赖于在`AppProvider`中定义的多个依赖项，例如 React Query 上下文、通知等。提供`AppProvider`作为`wrapper`将在我们测试期间渲染组件时使其可用。

+   `checkTableValues`是一个函数，它遍历表格中的所有单元格，并将每个值与提供的数据中的相应值进行比较，确保所有信息都显示在表格中。

+   `waitForLoadingToFinish`是一个函数，它在我们可以继续进行测试之前等待所有加载旋转器消失。这在我们必须等待某些数据被获取后才能断言值时很有用。

另一个值得提及的文件是`src/testing/setup-tests.ts`，在那里我们可以配置不同的初始化和清理动作。在我们的案例中，它帮助我们初始化和重置测试之间的模拟 API。

我们可以根据页面拆分我们的集成测试，并测试每个页面上的所有部分。想法是在以下部分对我们的应用程序进行集成测试：

+   仪表板工作页面

+   仪表板工作页面

+   创建工作页面

+   登录页面

+   公共工作页面

+   公共组织页面

## 仪表板工作页面

仪表板作业页面的功能基于当前登录用户。在这里，我们正在获取用户组织的所有作业并在作业表中显示它们。

让我们从打开`src/__tests__/dashboard-jobs-page.test.tsx`文件并添加以下内容开始：

```js
import DashboardJobsPage from '@/pages/dashboard/jobs';
import { getUser } from '@/testing/mocks/utils';
import { testData } from '@/testing/test-data';
import {
  appRender,
  checkTableValues,
  screen,
  waitForLoadingToFinish,
} from '@/testing/test-utils';
// 1
jest.mock('@/features/auth', () => ({
  useUser: () => ({ data: getUser() }),
}));
describe('Dashboard Jobs Page', () => {
  it('should render the jobs list', async () => {
    // 2
    await appRender(<DashboardJobsPage />);
    // 3
    expect(screen.getByText(/jobs/i)).toBeInTheDocument();
    // 4
    await waitForLoadingToFinish();
    // 5
    checkTableValues({
      container: screen.getByTestId('jobs-list'),
      data: testData.jobs,
      columns: ['position', 'department', 'location'],
    });
  });
});
```

测试工作如下：

1.  由于加载作业依赖于当前登录用户，我们需要模拟`useUser`钩子以返回正确的用户对象。

1.  然后，我们渲染页面。

1.  然后，我们确保作业页面的标题显示在页面上。

1.  要获取已加载的作业，我们需要等待它们加载完成。

1.  最后，我们断言表格中的作业值。

## 仪表板作业页面

仪表板作业页面的功能是我们希望加载作业数据并在页面上显示它。

让我们从打开`src/__tests__/dashboard-job-page.test.tsx`文件并添加以下内容开始：

```js
import DashboardJobPage from '@/pages/dashboard/jobs/
  [jobId]';
import { testData } from '@/testing/test-data';
import {
  appRender,
  screen,
  waitForLoadingToFinish,
} from '@/testing/test-utils';
const job = testData.jobs[0];
const router = {
  query: {
    jobId: job.id,
  },
};
// 1
jest.mock('next/router', () => ({
  useRouter: () => router,
}));
describe('Dashboard Job Page', () => {
  it('should render all the job details', async () => {
    // 2
    await appRender(<DashboardJobPage />);
    await waitForLoadingToFinish();
    const jobPosition = screen.getByRole('heading', {
      name: job.position,
    });
    const info = screen.getByText(job.info);
    // 3
    expect(jobPosition).toBeInTheDocument();
    expect(info).toBeInTheDocument();
  });
});
```

测试工作如下：

1.  由于我们是根据`jobId` URL 参数加载作业数据，我们需要模拟`useRouter`钩子以返回正确的作业 ID。

1.  然后，我们渲染页面并等待数据加载，通过等待页面上所有加载器消失来实现。

1.  最后，我们检查作业数据是否显示在页面上。

## 作业创建页面

作业创建页面包含一个表单，当提交时，它调用 API 端点在后端创建一个新的作业。当请求成功时，我们将用户重定向到仪表板作业页面并显示关于成功创建作业的通知。

让我们从打开`src/__tests__/dashboard-create-job-page.test.tsx`文件并添加以下内容开始：

```js
import DashboardCreateJobPage from '@/pages/dashboard/jobs/
  create';
import {
  appRender,
  screen,
  userEvent,
  waitFor,
} from '@/testing/test-utils';
const router = {
  push: jest.fn(),
};
// 1
jest.mock('next/router', () => ({
  useRouter: () => router,
}));
const jobData = {
  position: 'Software Engineer',
  location: 'London',
  department: 'Engineering',
  info: 'Lorem Ipsum',
};
describe('Dashboard Create Job Page', () => {
  it('should create a new job', async () => {
    // 2
    appRender(<DashboardCreateJobPage />);
    const positionInput = screen.getByRole('textbox', {
      name: /position/i,
    });
    const locationInput = screen.getByRole('textbox', {
      name: /location/i,
    });
    const departmentInput = screen.getByRole('textbox', {
      name: /department/i,
    });
    const infoInput = screen.getByRole('textbox', {
      name: /info/i,
    });
    const submitButton = screen.getByRole('button', {
      name: /create/i,
    });
    // 3
    userEvent.type(positionInput, jobData.position);
    userEvent.type(locationInput, jobData.location);
    userEvent.type(departmentInput, jobData.department);
    userEvent.type(infoInput, jobData.info);
    // 4
    userEvent.click(submitButton);
    // 5
    await waitFor(() =>
      expect(
        screen.getByText(/job created!/i)
      ).toBeInTheDocument()
    );
  });
});
```

测试工作如下：

1.  首先，我们需要模拟`useRouter`钩子以包含`push`方法，因为提交后它用于导航到作业页面。

1.  然后，我们渲染页面组件。

1.  之后，我们将所有输入值插入到它们中。

1.  然后，我们通过模拟**提交**按钮上的点击事件来提交表单。

1.  提交后，我们需要等待文档中显示**作业已创建**的通知。

## 公共组织页面

对于组织页面，由于我们是在服务器上渲染它，我们需要在服务器上获取数据并在页面上显示。

让我们从打开`src/__tests__/public-organization-page.test.tsx`文件并定义测试套件的骨架开始，如下所示：

```js
import PublicOrganizationPage, {
  getServerSideProps,
} from '@/pages/organizations/[organizationId]';
import { testData } from '@/testing/test-data';
import {
  appRender,
  checkTableValues,
  screen,
} from '@/testing/test-utils';
const organization = testData.organizations[0];
const jobs = testData.jobs;
describe('Public Organization Page', () => {
  it('should use getServerSideProps that fetches and
    returns the proper data', async () => {
  });
  it('should render the organization details', async () => {
  });
  it('should render the not found message if the
    organization is not found', async () => {
  });
});
```

现在，我们将关注测试套件中的每个测试。

首先，我们想要测试`getServerSideProps`函数获取正确的数据并将其作为 props 返回，这些 props 将在页面上提供：

```js
it('should use getServerSideProps that fetches and returns
  the proper data', async () => {
  const { props } = await getServerSideProps({
    params: {
      organizationId: organization.id,
    },
  } as any);
  expect(props.organization).toEqual(organization);
  expect(props.jobs).toEqual(jobs);
});
```

在这里，我们正在调用`getServerSideProps`函数并断言返回的值包含相应的数据。

在第二个测试中，我们想要验证提供给`PublicOrganizationPage`组件的 props 是否正确渲染：

```js
it('should render the organization details', async () => {
  appRender(
    <PublicOrganizationPage
      organization={organization}
      jobs={jobs}
    />
  );
  expect(
    screen.getByRole('heading', {
      name: organization.name,
    })
  ).toBeInTheDocument();
  expect(
    screen.getByRole('heading', {
      name: organization.email,
    })
  ).toBeInTheDocument();
  expect(
    screen.getByRole('heading', {
      name: organization.phone,
    })
  ).toBeInTheDocument();
  checkTableValues({
    container: screen.getByTestId('jobs-list'),
    data: jobs,
    columns: ['position', 'department', 'location'],
  });
});
```

在这个测试中，我们正在渲染页面组件并验证所有值是否显示在页面上。

在测试套件的第三个测试中，我们想要断言如果组织不存在，我们想要显示*未找到*消息：

```js
it('should render the not found message if the organization is not found', async () => {
  appRender(
    <PublicOrganizationPage
      organization={null}
      jobs={[]}
    />
  );
  const notFoundMessage = screen.getByRole('heading', {
    name: /not found/i,
  });
  expect(notFoundMessage).toBeInTheDocument();
});
```

在这里，我们正在渲染`PublicOrganizationPage`组件，并使用`null`的组织值，然后验证*未找到*消息是否在文档中。

## 公共职位页面

对于公共职位页面，由于我们在服务器上渲染它，我们需要在服务器上获取数据并在页面上显示它。

让我们从打开`src/__tests__/public-job-page.test.tsx`文件并定义测试的框架开始：

```js
import PublicJobPage, {
  getServerSideProps,
} from '@/pages/organizations/[organizationId]/jobs/[jobId]';
import { testData } from '@/testing/test-data';
import { appRender, screen } from '@/testing/test-utils';
const job = testData.jobs[0];
const organization = testData.organizations[0];
describe('Public Job Page', () => {
  it('should use getServerSideProps that fetches and
    returns the proper data', async () => {
  });
  it('should render the job details', async () => {
  });
  it('should render the not found message if the data does
    not exist', async () => {
  });
});
```

现在，我们可以专注于测试套件中的每个测试。

首先，我们需要测试`getServerSideProps`函数，它将获取数据并通过 props 将数据返回到页面：

```js
it('should use getServerSideProps that fetches and returns
  the proper data', async () => {
  const { props } = await getServerSideProps({
    params: {
      jobId: job.id,
      organizationId: organization.id,
    },
  } as any);
  expect(props.job).toEqual(job);
  expect(props.organization).toEqual(organization);
});
```

在这里，我们调用`getServerSideProps`并断言返回值是否与预期数据匹配。

现在，我们可以测试`PublicJobPage`，我们想要确保提供的数据显示在页面上：

```js
it('should render the job details', async () => {
  appRender(
    <PublicJobPage
      organization={organization}
      job={job}
    />
  );
  const jobPosition = screen.getByRole('heading', {
    name: job.position,
  });
  const info = screen.getByText(job.info);
  expect(jobPosition).toBeInTheDocument();
  expect(info).toBeInTheDocument();
});
```

在这里，我们渲染页面组件并验证提供的职位数据是否显示在页面上。

最后，我们要断言`getServerSideProps`提供的数据不存在的情况：

```js
it('should render the not found message if the data does not exist', async () => {
  const { rerender } = appRender(
    <PublicJobPage organization={null} job={null} />
  );
  const notFoundMessage = screen.getByRole('heading', {
    name: /not found/i,
  });
  expect(notFoundMessage).toBeInTheDocument();
  rerender(
    <PublicJobPage
      organization={organization}
      job={null}
    />
  );
  expect(notFoundMessage).toBeInTheDocument();
  rerender(
    <PublicJobPage organization={null} job={job} />
  );
  expect(notFoundMessage).toBeInTheDocument();
  rerender(
    <PublicJobPage
      organization={organization}
      job={{ ...job, organizationId: '123' }}
    />
  );
  expect(notFoundMessage).toBeInTheDocument();
});
```

由于存在多个数据可能被视为无效的情况，我们使用了`rerender`函数，它可以使用不同的 props 集重新渲染组件。我们断言如果数据未找到，则*未找到*消息将在页面上显示。

## 登录页面

登录页面渲染登录表单，当成功提交时，将用户导航到仪表板。

让我们从打开`src/__tests__/login-page.test.tsx`文件并添加以下内容开始：

```js
import LoginPage from '@/pages/auth/login';
import {
  appRender,
  screen,
  userEvent,
  waitFor,
} from '@/testing/test-utils';
// 1
const router = {
  replace: jest.fn(),
  query: {},
};
jest.mock('next/router', () => ({
  useRouter: () => router,
}));
describe('Login Page', () => {
  it('should login the user into the dashboard', async () => {
    // 2
    await appRender(<LoginPage />);
    const emailInput = screen.getByRole('textbox', {
      name: /email/i,
    });
    const passwordInput =
      screen.getByLabelText(/password/i);
    const submitButton = screen.getByRole('button', {
      name: /log in/i,
    });
    const credentials = {
      email: 'user1@test.com',
      password: 'password',
    };
    // 3
    userEvent.type(emailInput, credentials.email);
    userEvent.type(passwordInput, credentials.password);
    userEvent.click(submitButton);
    // 4
    await waitFor(() =>
      expect(router.replace).toHaveBeenCalledWith(
        '/dashboard/jobs'
      )
    );
  });
});
```

测试工作如下：

1.  我们需要模拟`useRouter`钩子，因为它被用来在成功提交时将用户导航到仪表板。

1.  接下来，我们渲染页面。

1.  然后，我们将凭据输入到表单中并提交。

1.  最后，我们期望在路由器上的`replace`方法被调用，并带有`/dashboard/jobs`值，如果登录提交成功，则应将用户导航到仪表板。

要运行集成测试，我们可以执行以下命令：

```js
npm run test
```

如果我们想观察测试中的变化，我们可以执行以下命令：

```js
npm run test:watch
```

# 端到端测试

端到端测试是一种将应用程序作为一个完整实体进行测试的测试方法。通常，这些测试包括以自动化方式运行整个应用程序，包括前端和后端，并验证整个系统是否正常工作。

在端到端测试中，我们通常想要测试成功路径，以确认一切按预期工作。

为了测试我们的应用程序端到端，我们将使用 Cypress，这是一个非常流行的测试框架，它通过在无头浏览器中执行测试来工作。这意味着测试将在真实的浏览器环境中运行。除了 Cypress 之外，由于我们已经熟悉了 React Testing Library，我们将使用 Cypress 的 Testing Library 插件来与页面交互。

对于我们的应用程序，我们想要测试两个应用程序的流程：

+   仪表板流程

+   公共流程

## 仪表板流程

仪表板流程是组织管理员想要测试用户认证以及访问和交互仪表板不同部分的流程。

让我们从打开`cypress/e2e/dashboard.cy.ts`文件并添加我们的测试骨架开始：

```js
import { testData } from '../../src/testing/test-data';
const user = testData.users[0];
const job = testData.jobs[0];
describe('dashboard', () => {
  it('should authenticate into the dashboard', () => {
  });
  it('should navigate to and visit the job details page', () => {
  });
  it('should create a new job', () => {
  });
  it('should log out from the dashboard', () => {
  });
});
```

现在，让我们来实现测试。

首先，我们想要认证进入仪表板：

```js
it('should authenticate into the dashboard', () => {
  cy.clearCookies();
  cy.clearLocalStorage();
  cy.visit('http://localhost:3000/dashboard/jobs');
  cy.wait(500);
  cy.url().should(
    'equal',
    'http://localhost:3000/auth/login?redirect=/dashboard/
      jobs'
  );
  cy.findByRole('textbox', {
    name: /email/i,
  }).type(user.email);
  cy.findByLabelText(/password/i).type(
    user.password.toLowerCase()
  );
  cy.findByRole('button', {
    name: /log in/i,
  }).click();
  cy.findByRole('heading', {
    name: /jobs/i,
  }).should('exist');
});
```

在这里，我们想要清除 cookies 和`localStorage`。然后，我们必须尝试导航到仪表板；然而，应用程序将重定向我们到登录页面。我们必须在登录表单中输入凭据并提交。之后，我们将被重定向到仪表板职位页面，在那里我们可以看到**职位**标题。

现在我们处于仪表板职位页面，我们可以通过访问职位详情页面来进一步操作：

```js
it('should navigate to and visit the job details page', () => {
  cy.findByRole('row', {
    name: new RegExp(
      `${job.position} ${job.department} ${job.location}
        View`,
      'i'
    ),
  }).within(() => {
    cy.findByRole('link', {
      name: /view/i,
    }).click();
  });
  cy.findByRole('heading', {
    name: job.position,
  }).should('exist');
  cy.findByText(new RegExp(job.info, 'i')).should(
    'exist'
  );
});
```

在这里，我们点击了其中一个职位的**查看**链接，并导航到职位详情页面，以验证所选的职位数据是否显示在页面上。

现在，让我们测试职位创建过程：

```js
it('should create a new job', () => {
  cy.go('back');
  cy.findByRole('link', {
    name: /create job/i,
  }).click();
  const jobData = {
    position: 'Software Engineer',
    location: 'London',
    department: 'Engineering',
    info: 'Lorem Ipsum',
  };
  cy.findByRole('textbox', {
    name: /position/i,
  }).type(jobData.position);
  cy.findByRole('textbox', {
    name: /department/i,
  }).type(jobData.department);
  cy.findByRole('textbox', {
    name: /location/i,
  }).type(jobData.location);
  cy.findByRole('textbox', {
    name: /info/i,
  }).type(jobData.info);
  cy.findByRole('button', {
    name: /create/i,
  }).click();
  cy.findByText(/job created!/i).should('exist');
});
```

由于我们处于职位详情页面，我们需要导航回仪表板职位页面，在那里我们可以点击**创建职位**链接。这将带我们到创建职位页面。在这里，我们填写表格并提交。当提交成功时，应该会显示**职位已创建**的通知。

现在我们已经测试了仪表板的所有功能，我们可以从仪表板注销：

```js
it('should log out from the dashboard', () => {
  cy.findByRole('button', {
    name: /log out/i,
  }).click();
  cy.wait(500);
  cy.url().should(
    'equal',
    'http://localhost:3000/auth/login'
  );
});
```

点击**注销**按钮将用户注销并重定向到登录页面。

## 公共流程

应用程序的公共流程对访问它的每个人都是可用的。

让我们从打开`cypress/e2e/public.cy.ts`文件并添加测试的骨架开始：

```js
import { testData } from '../../src/testing/test-data';
const organization = testData.organizations[0];
const job = testData.jobs[0];
describe('public application flow', () => {
  it('should display the organization public page', () => {
  });
  it('should navigate to and display the public job details
    page', () => {
  });
});
```

现在，让我们开始实现测试。

首先，我们想要访问组织页面：

```js
it('should display the organization public page', () => {
  cy.visit(
    `http://localhost:3000/organizations/${organization.id}`
  );
  cy.findByRole('heading', {
    name: organization.name,
  }).should('exist');
  cy.findByRole('heading', {
    name: organization.email,
  }).should('exist');
  cy.findByRole('heading', {
    name: organization.phone,
  }).should('exist');
  cy.findByText(
    new RegExp(organization.info, 'i')
  ).should('exist');
});
```

在这里，我们正在访问组织详情页面，并检查显示的数据是否与组织匹配。

现在我们处于组织详情页面，我们可以查看组织的职位：

```js
it('should navigate to and display the public job details
  page', () => {
  cy.findByTestId('jobs-list').should('exist');
  cy.findByRole('row', {
    name: new RegExp(
      `${job.position} ${job.department} ${job.location}
        View`,
      'i'
    ),
  }).within(() => {
    cy.findByRole('link', {
      name: /view/i,
    }).click();
  });
  cy.url().should(
    'equal',
    `http://localhost:3000/organizations/$
      {organization.id}/jobs/${job.id}`
  );
  cy.findByRole('heading', {
    name: job.position,
  }).should('exist');
  cy.findByText(new RegExp(job.info, 'i')).should(
    'exist'
  );
});
```

在这里，我们点击了职位的**查看**链接，然后导航到职位详情页面，在这里我们断言职位数据。

要运行端到端测试，我们需要首先通过运行以下命令来构建应用程序：

```js
npm run build
```

然后，我们可以通过打开浏览器来开始测试：

```js
npm run e2e
```

或者，我们可以以无头模式运行测试，因为它对资源的需求较少，这对于 CI 来说非常好：

```js
npm run e2e:headless
```

# 摘要

在本章中，我们学习了如何测试我们的应用程序，使其准备好投入生产。

我们首先通过为我们的通知存储实现单元测试来学习单元测试。

由于集成测试非常有价值，因为它们提供了更多的信心，表明某些东西正在正常工作，我们使用了这些测试来测试页面。

最后，我们为公共和仪表板流程创建了端到端测试，其中我们测试了每个流程的整个功能。

在下一章中，我们将学习如何准备和发布我们的应用程序到生产环境。我们将使用这些测试并将它们集成到我们的 CI/CD 管道中，如果任何测试失败，我们将不允许应用程序发布到生产环境。这将使我们的用户更加满意，因为出现错误最终进入生产环境的可能性更小。
