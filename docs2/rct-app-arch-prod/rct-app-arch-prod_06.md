# 6

# 将 API 集成到应用程序中

在上一章中，我们介绍了设置模拟 API，这是我们将在应用程序中消费的 API。

在本章中，我们将学习如何通过应用程序消费 API。

当我们说 API 时，我们指的是 API 后端服务器。我们将学习如何从客户端和服务器获取数据。对于 HTTP 客户端，我们将使用**Axios**，而对于处理获取的数据，我们将使用**React Query**库，它允许我们在 React 应用程序中处理 API 请求和响应。

在本章中，我们将涵盖以下主题：

+   配置 API 客户端

+   配置 React Query

+   为功能创建 API 层

+   在应用程序中使用 API 层

到本章结束时，我们将知道如何以干净和有序的方式使我们的应用程序与 API 进行通信。

# 技术要求

在我们开始之前，我们需要设置我们的项目。为了能够开发我们的项目，我们需要在计算机上安装以下内容：

+   **Node.js**版本 16 或以上和**npm**版本 8 或以上

安装 Node.js 和 npm 有多种方式。这里有一篇很好的文章，详细介绍了更多细节：[`www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js`](https://www.nodejsdesignpatterns.com/blog/5-ways-to-install-node-js)。

+   **VSCode**（可选）目前是 JavaScript/TypeScript 最受欢迎的编辑器/IDE，因此我们将使用它。它是开源的，与 TypeScript 有很好的集成，并且我们可以通过扩展来扩展其功能。可以从[`code.visualstudio.com/`](https://code.visualstudio.com/)下载。

本章的代码文件可以在以下位置找到：[`github.com/PacktPublishing/React-Application-Architecture-for-Production`](https://github.com/PacktPublishing/React-Application-Architecture-for-Production)

可以使用以下命令在本地克隆仓库：

```js
git clone https://github.com/PacktPublishing/React-Application-Architecture-for-Production.git
```

一旦克隆了仓库，我们需要安装应用程序的依赖项：

```js
npm install
```

我们可以使用以下命令提供环境变量：

```js
cp .env.example .env
```

依赖项安装完成后，我们需要选择与本章匹配的代码库的正确阶段。我们可以通过执行以下命令来完成：

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

这是第六章，所以如果我们想跟随着学习，可以选择`chapter-06-start`，或者选择`chapter-06`来查看本章的最终结果。

一旦选择了章节，所有跟随本章所需的文件都将出现。

更多关于设置细节的信息，请查看`README.md`文件。

# 配置 API 客户端

对于我们应用程序的 API 客户端，我们将使用 Axios，这是一个用于处理 HTTP 请求的非常流行的库。它在浏览器和服务器上都得到支持，并提供创建实例、拦截请求和响应、取消请求等功能。

让我们先创建一个 Axios 实例，这将包括我们希望在每次请求中完成的常见操作。

创建 `src/lib/api-client.ts` 文件并添加以下内容：

```js
import Axios from 'axios';
import { API_URL } from '@/config/constants';
export const apiClient = Axios.create({
  baseURL: API_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});
apiClient.interceptors.response.use(
  (response) => {
    return response.data;
  },
  (error) => {
    const message =
      error.response?.data?.message || error.message;
    console.error(message);
    return Promise.reject(error);
  }
);
```

在这里，我们创建了一个 Axios 实例，其中我们定义了一个公共基本 URL 和我们希望在每次请求中包含的标头。

然后，我们在想要提取数据属性并返回给客户端的地方附加了一个响应拦截器。我们还定义了错误拦截器，其中我们想要将错误记录到控制台。

然而，拥有一个配置好的 Axios 实例并不足以优雅地处理 React 组件中的请求。我们仍然需要处理调用 API、等待数据到达以及将其存储在状态中的操作。这就是 React Query 发挥作用的地方。

# 配置 React Query

React Query 是一个处理异步数据和使其在 React 组件中可用的优秀库。

## 为什么选择 React Query？

React Query 是处理异步远程状态的一个很好的选择，主要原因是它为我们处理了很多事情。

想象以下组件，它从 API 加载数据并显示：

```js
const loadData = () => Promise.resolve('data');
const DataComponent = () => {
  const [data, setData] = useState();
  const [error, setError] = useState();
  const [isLoading, setIsLoading] = useState();
  useEffect(() => {
    setIsLoading(true);
    loadData()
      .then((data) => {
        setData(data);
      })
      .catch((error) => {
        setError(error);
      })
      .finally(() => {
        setIsLoading(false);
      });
  }, []);
  if (isLoading) return <div>Loading</div>;
  if (error) return <div>{error}</div>;
  return <div>{data}</div>;
};
```

如果我们只从 API 获取一次数据，这没问题，但在大多数情况下，我们需要从许多不同的端点获取数据。我们可以看到这里有一些样板代码：

+   需要定义相同的 `data`、`error` 和 `isLoading` 状态片段

+   必须相应地更新不同的状态片段

+   当我们离开组件时，数据就会被丢弃

这就是 React Query 发挥作用的地方。我们可以将我们的组件更新为以下内容：

```js
import { useQuery } from '@tanstack/react-query';
const loadData = () => Promise.resolve('data');
const DataComponent = () => {
  const {data, error, isLoading} = useQuery({
    queryFn: loadData,
    queryKey: ['data']
  })
  if (isLoading) return <div>Loading</div>;
  if (error) return <div>{error}</div>;
  return <div>{data}</div>;
};
```

注意状态处理是如何从消费者中抽象出来的。我们不需要担心存储数据，或处理加载和错误状态；一切由 React Query 处理。React Query 的另一个好处是其缓存机制。对于每个查询，我们需要提供一个相应的查询键，该键将用于在缓存中存储数据。

这也有助于请求的去重。如果我们从多个地方调用相同的查询，它会确保 API 请求只发生一次。

## 配置 React Query

现在，回到我们的应用程序。我们已经有 `react-query` 安装了。我们只需要为我们的应用程序配置它。配置需要一个查询客户端，我们可以在 `src/lib/react-query.ts` 中创建它并添加以下内容：

```js
import { QueryClient } from '@tanstack/react-query';
export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: false,
      refetchOnWindowFocus: false,
      useErrorBoundary: true,
    },
  },
});
```

React Query 在创建查询客户端时提供了一个默认配置，我们可以在创建过程中覆盖它。完整的选项列表可以在文档中找到。

现在我们已经创建了我们的查询客户端，我们必须将其包含在提供者中。让我们前往 `src/providers/app.tsx` 并将内容替换为以下内容：

```js
import {
  ChakraProvider,
  GlobalStyle,
} from '@chakra-ui/react';
import { QueryClientProvider } from '@tanstack/
  react-query';
import { ReactQueryDevtools } from '@tanstack/
  react-query-devtools';
import { ReactNode } from 'react';
import { ErrorBoundary } from 'react-error-boundary';
import { theme } from '@/config/theme';
import { queryClient } from '@/lib/react-query';
type AppProviderProps = {
  children: ReactNode;
};
export const AppProvider = ({
  children,
}: AppProviderProps) => {
  return (
    <ChakraProvider theme={theme}>
      <ErrorBoundary
        fallback={<div>Something went wrong!</div>}
        onError={console.error}
      >
        <GlobalStyle />
        <QueryClientProvider client={queryClient}>
          <ReactQueryDevtools initialIsOpen={false} />
          {children}
        </QueryClientProvider>
      </ErrorBoundary>
    </ChakraProvider>
  );
};
```

在这里，我们正在导入并添加 `QueryClientProvider`，这将使查询客户端及其配置可用于查询和突变。注意我们如何将查询客户端实例作为 `client` 属性传递。

我们还添加了`ReactQueryDevtools`，这是一个允许我们检查所有查询的小部件。它仅在开发中使用，这对于调试非常有用。

现在我们已经设置了`react-query`，我们可以开始实现功能的 API 层。

# 定义功能的 API 层

API 层将在每个功能的`api`文件夹中定义。一个 API 请求可以是查询或突变。查询描述了仅获取数据的请求。突变描述了一个在服务器上修改数据的 API 调用。

对于每个 API 请求，我们都会有一个包含并导出 API 请求定义函数和用于在 React 中消费请求的钩子的文件。对于请求定义函数，我们将使用我们刚刚用 Axios 创建的 API 客户端，对于钩子，我们将使用 React Query 的钩子。

我们将在接下来的章节中学习如何实际实现它。

## 工作

对于`jobs`功能，我们有三个 API 调用：

+   `GET /jobs`

+   `GET /jobs/:jobId`

+   `POST /jobs`

### 获取工作

让我们从获取工作的 API 调用开始。为了在我们的应用程序中定义它，让我们创建`src/features/jobs/api/get-jobs.ts`文件并添加以下内容：

```js
import { useQuery } from '@tanstack/react-query';
import { apiClient } from '@/lib/api-client';
import { Job } from '../types';
type GetJobsOptions = {
  params: {
    organizationId: string | undefined;
  };
};
export const getJobs = ({
  params,
}: GetJobsOptions): Promise<Job[]> => {
  return apiClient.get('/jobs', {
    params,
  });
};
export const useJobs = ({ params }: GetJobsOptions) => {
  const { data, isFetching, isFetched } = useQuery({
    queryKey: ['jobs', params],
    queryFn: () => getJobs({ params }),
    enabled: !!params.organizationId,
    initialData: [],
  });
  return {
    data,
    isLoading: isFetching && !isFetched,
  };
};
```

如我们所见，这里发生了一些事情：

1.  我们正在定义请求选项的类型。在那里，我们可以传递`organizationId`来指定我们想要获取工作的组织。

1.  我们正在定义`getJobs`函数，这是获取工作的请求定义。

1.  我们通过使用`react-query`的`useQuery`钩子来定义`useJobs`钩子。`useQuery`钩子返回许多不同的属性，但我们只想暴露应用程序所需的内容。注意，通过使用`enabled`属性，我们正在告诉`useQuery`只有在`organizationId`提供时才运行。这意味着查询将在获取数据之前等待`organizationId`存在。

由于我们将在功能外部使用它，让我们在`src/features/jobs/index.ts`中使其可用：

```js
export * from './api/get-jobs';
```

### 获取工作详情

获取工作请求应该是直接的。让我们创建`src/features/jobs/api/get-job.ts`文件并添加以下内容：

```js
import { useQuery } from '@tanstack/react-query';
import { apiClient } from '@/lib/api-client';
import { Job } from '../types';
type GetJobOptions = {
  jobId: string;
};
export const getJob = ({
  jobId,
}: GetJobOptions): Promise<Job> => {
  return apiClient.get(`/jobs/${jobId}`);
};
export const useJob = ({ jobId }: GetJobOptions) => {
  const { data, isLoading } = useQuery({
    queryKey: ['jobs', jobId],
    queryFn: () => getJob({ jobId }),
  });
  return { data, isLoading };
};
```

如我们所见，我们正在定义和导出`getJob`函数和`useJob`查询，我们将在稍后使用它们。

我们希望在功能外部使用这个 API 请求，因此我们必须通过从`src/features/jobs/index.ts`重新导出它来使其可用：

```js
export * from './api/get-job';
```

### 创建工作

正如我们已经提到的，每当我们在服务器上更改某些内容时，都应该将其视为突变。有了这个，让我们创建`src/features/jobs/api/create-job.ts`文件并添加以下内容：

```js
import { useMutation } from '@tanstack/react-query';
import { apiClient } from '@/lib/api-client';
import { queryClient } from '@/lib/react-query';
import { Job, CreateJobData } from '../types';
type CreateJobOptions = {
  data: CreateJobData;
};
export const createJob = ({
  data,
}: CreateJobOptions): Promise<Job> => {
  return apiClient.post(`/jobs`, data);
};
type UseCreateJobOptions = {
  onSuccess?: (job: Job) => void;
};
export const useCreateJob = ({
  onSuccess,
}: UseCreateJobOptions = {}) => {
  const { mutate: submit, isLoading } = useMutation({
    mutationFn: createJob,
    onSuccess: (job) => {
      queryClient.invalidateQueries(['jobs']);
      onSuccess?.(job);
    },
  });
  return { submit, isLoading };
};
```

这里发生了一些事情：

1.  我们定义了 API 请求的`CreateJobOptions`类型。它将需要一个包含创建新工作所需所有字段的数据对象。

1.  我们定义了`createJob`函数，它向服务器发送请求。

1.  我们定义了`UseCreateJobOptions`，它接受一个可选的回调函数，在请求成功时调用。这在我们想要显示通知、重定向用户或执行与 API 请求无直接关系的事情时可能很有用。

1.  我们正在定义`useCreateJob`钩子，它使用`react-query`中的`useMutation`。如类型定义中所述，它接受一个可选的`onSuccess`回调，如果突变成功则被调用。

1.  要创建突变，我们将`createJob`函数作为`mutationFn`提供。

1.  我们定义`useMutation`的`onSuccess`，在新工作创建后，我们使所有工作查询无效。使查询无效意味着我们想要在缓存中将它们设置为无效。如果我们再次需要它们，我们必须从 API 中获取它们。

1.  我们正在减少`useCreateJob`钩子的 API 表面，只暴露那些应用程序使用的功能，所以我们只暴露`submit`和`isLoading`。如果我们注意到我们需要更多东西，我们总是可以在未来暴露更多东西。

由于它只用于`jobs`功能内部，我们不需要从`index.ts`文件中导出这个请求。

## 组织

对于`organizations`功能，我们有一个 API 调用：

+   `GET /organizations/:organizationId`

### 获取组织详情

让我们创建`src/features/organizations/api/get-organization.ts`并添加以下内容：

```js
import { useQuery } from '@tanstack/react-query';
import { apiClient } from '@/lib/api-client';
import { Organization } from '../types';
type GetOrganizationOptions = {
  organizationId: string;
};
export const getOrganization = ({
  organizationId,
}: GetOrganizationOptions): Promise<Organization> => {
  return apiClient.get(
    `/organizations/${organizationId}`
  );
};
export const useOrganization = ({
  organizationId,
}: GetOrganizationOptions) => {
  const { data, isLoading } = useQuery({
    queryKey: ['organizations', organizationId],
    queryFn: () => getOrganization({ organizationId }),
  });
  return { data, isLoading };
};
```

在这里，我们定义了一个查询，它将根据我们传递的`organizationId`属性获取组织。

由于这个查询也将被`organizations`功能外部使用，让我们也从`src/features/organizations/index.ts`中重新导出：

```js
export * from './api/get-organization';
```

现在我们已经定义了所有的 API 请求，我们可以在我们的应用程序中开始使用它们了。

# 在应用程序中消费 API

为了能够在没有 API 功能的情况下构建 UI，我们在我们的页面上使用了测试数据。现在，我们想要用我们刚刚为与 API 通信而制作的真实查询和突变来替换它。

## 公共组织

我们现在需要替换一些东西。

让我们打开`src/pages/organizations/[organizationId]/index.tsx`并删除以下内容：

```js
import {
  getJobs,
  getOrganization,
} from '@/testing/test-data';
```

现在，我们必须从 API 加载数据。我们可以通过从相应的`features`中导入`getJobs`和`getOrganization`来实现这一点。让我们添加以下内容：

```js
import { JobsList, Job, getJobs } from '@/features/jobs';
import {
  getOrganization,
  OrganizationInfo,
} from '@/features/organizations';
```

新的 API 函数略有不同，所以我们需要替换以下代码：

```js
const [organization, jobs] = await Promise.all([
  getOrganization(organizationId).catch(() => null),
  getJobs(organizationId).catch(() => [] as Job[]),
]);
```

我们必须用以下内容替换它：

```js
const [organization, jobs] = await Promise.all([
  getOrganization({ organizationId }).catch(() => null),
  getJobs({
    params: {
      organizationId: organizationId,
    },
  }).catch(() => [] as Job[]),
]);
```

## 公共工作

对于公共工作页面，应该重复相同的过程。

让我们打开`src/pages/organizations/[organizationId]/jobs/[jobId].tsx`并删除以下内容：

```js
import {
  getJob,
  getOrganization,
} from '@/testing/test-data';
```

现在，让我们从相应的功能中导入`getJob`和`getOrganization`。

```js
import { getJob, PublicJobInfo } from '@/features/jobs';
import { getOrganization } from '@/features/organizations';
```

然后，在`getServerSideProps`内部，我们需要更新以下内容：

```js
const [organization, job] = await Promise.all([
  getOrganization({ organizationId }).catch(() => null),
  getJob({ jobId }).catch(() => null),
]);
```

## 仪表板工作

对于仪表板工作，我们唯一需要做的事情是更新导入，这样我们就不再从测试数据中加载工作，而是从 API 中加载。

让我们通过更新`src/pages/dashboard/jobs/index.tsx`中的以下行来从`jobs`功能导入`useJobs`而不是测试数据：

```js
import { JobsList, useJobs } from '@/features/jobs';
import { useUser } from '@/testing/test-data';
```

目前我们仍然会保留来自 `test-data` 的 `useUser`；我们将在下一章替换它。

由于新创建的 `useJobs` 钩子与 `test-data` 中的钩子略有不同，我们需要更新其使用方式，如下所示：

```js
const jobs = useJobs({
  params: {
    organizationId: user.data?.organizationId ?? '',
  },
});
```

## 仪表板工作

仪表板中的工作详情页面也非常简单。

在 `src/pages/dashboard/jobs/[jobId].tsx` 文件中，让我们移除从 `test-data` 导入的 `useJob`：

```js
import { useJob } from '@/testing/test-data';
```

现在，让我们从 `jobs` 功能中导入它：

```js
import {
  DashboardJobInfo,
  useJob,
} from '@/features/jobs';
```

在这里，我们需要更新 `useJob` 的使用方式：

```js
const job = useJob({ jobId });
```

## 创建工作

对于工作创建，我们需要更新表单，当提交时，将创建一个新的工作。

目前，表单不可用，因此我们需要添加一些内容。

打开 `src/features/jobs/components/create-job-form/create-job-form.tsx` 文件，并将内容替换为以下内容：

```js
import { Box, Stack } from '@chakra-ui/react';
import { useForm } from 'react-hook-form';
import { Button } from '@/components/button';
import { InputField } from '@/components/form';
import { useCreateJob } from '../../api/create-job';
import { CreateJobData } from '../../types';
export type CreateJobFormProps = {
  onSuccess: () => void;
};
export const CreateJobForm = ({
  onSuccess,
}: CreateJobFormProps) => {
  const createJob = useCreateJob({ onSuccess });
  const { register, handleSubmit, formState } =
    useForm<CreateJobData>();
  const onSubmit = (data: CreateJobData) => {
    createJob.submit({ data });
  };
  return (
    <Box w="full">
      <Stack
        as="form"
        onSubmit={handleSubmit(onSubmit)}
        w="full"
        spacing="8"
      >
        <InputField
          label="Position"
          {...register('position', {
            required: 'Required',
          })}
          error={formState.errors['position']}
        />
        <InputField
          label="Department"
          {...register('department', {
            required: 'Required',
          })}
          error={formState.errors['department']}
        />
        <InputField
          label="Location"
          {...register('location', {
            required: 'Required',
          })}
          error={formState.errors['location']}
        />
        <InputField
          type="textarea"
          label="Info"
          {...register('info', {
            required: 'Required',
          })}
          error={formState.errors['info']}
        />
        <Button
          isDisabled={createJob.isLoading}
          isLoading={createJob.isLoading}
          type="submit"
        >
          Create
        </Button>
      </Stack>
    </Box>
  );
};
```

在这个组件中，有几个值得注意的点：

1.  我们正在使用 `useForm` 钩子来处理表单的状态。

1.  我们正在导入并使用之前定义的 `useCreateJob` API 钩子来提交请求。

1.  当突变成功时，会调用 `onSuccess` 回调。

注意

创建工作表单要求用户进行身份验证。由于我们尚未实现身份验证系统，您可以使用 MSW 开发工具使用测试用户进行身份验证以尝试表单提交。

# 摘要

在本章中，我们学习了如何使应用程序与其 API 进行通信。首先，我们定义了一个 API 客户端，它允许我们统一 API 请求。然后，我们介绍了 **React Query**，这是一个用于处理异步状态的库。使用它减少了样板代码并显著简化了代码库。

最后，我们声明了 API 请求，然后将其集成到应用程序中。

在下一章中，我们将学习如何为我们的应用程序创建一个身份验证系统，只有经过身份验证的用户才能访问仪表板。
