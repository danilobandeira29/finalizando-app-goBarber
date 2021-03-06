# 📝 Sobre
Anotações que faço ao longo dos estudos sobre:
- React Native
- Hooks (context api, useCallback, useMemo,...)
- API REST
- TDD(Test-Driven Development)

# 🏆 Desafio
- [x] Anotar a forma que resolvo os problemas, traçando caminhos e afins.
- [x] Colocar em prática os conhecimentos que são adquiridos diáriamente nos meus estudos.

# 👀 Projetos nos quais estou aplicando esses conceitos
Disponível em: [App GoBarber](https://github.com/danilobandeira29/mobile-gobarber)

---

## Endereços de imagens
1. Alterar a environment variable APP_API_URL para o ip da máquina.

> posso rodar o comando adb reverse tcp:3333 tcp:3333
> invalidar o cache para checar o *avatar_url*

## Criando páginas e rotas
1. Criar CreateAppointment/AppointmentCreated/Profile page.

## Header do Dashboard
1. Fazer a criação do header e estilizar.

## Buscando providers da API
1. Conectar com a API para listar os providers.
```typescript
import React, { useEffect, useState } from 'react';
import api from '../../services/api';

export interface Provider {
  id: string;
  name: string;
  avatar_url: string;
}

const Dashboard: React.FC = () => {
  const [providers, setProviders] = useState<Provider[]>([]);

  useEffect(() => {
    api.get('providers').then(response => setProviders(response.data));
  }, []);


  return (
    ...
  );
}
```

2. Fazer a estilização da FlatList
```typescript
import styled from 'styled-components/native';
import { FlatList } from 'react-native';

import { Provider } from './index';

export const ProvidersList = styled(Flatlist as new () => FlatList<Provider>)``;
```

3. Importar ProvidersList no index e exibir os nomes dos providers em tela.
```typescript
import React, { useEffect, useState } from 'react';
import api from '../../services/api';
import { ..., ProvidersList } from './styles';

export interface Provider {
  id: string;
  name: string;
  avatar_url: string;
}

const Dashboard: React.FC = () => {
  const [providers, setProviders] = useState<Provider[]>([]);

  useEffect(() => {
    api.get('providers').then(response => setProviders(response.data));
  }, []);


  return (
    ...
    <ProvidersList
      data={providers}
      keyExtractor={provider => provider.id}
      renderItem={({ item }) => (
        <UserName>{item.name}</UserName>
      )}
     />
  );
}
```
## Listagem de prestadores
1. Fazer a estilização da FlatList.
```typescript

const Dashboard: React.FC = () => {

  ...
  const { navigate } = useNavigation();

  const navigateToCreateAppointment = useCallback((providerId: string) => {
    navigate('CreateAppointment', { providerId });
  }, [navigate]);

return (...
  <ProvidersList
    data={providers}
    keyExtractor={provider => provider.id}
    ListHeaderComponent={
      <ProvidersListTitle>Cabeleireiros</ProvidersListTitle>
    }
    renderItem={({ item: provider }) => (
      <ProviderContainer
        onPress={() => navigateToCreateAppointment(provider.id)}
      >
        <ProviderAvatar source={{ uri: provider.avatar_url }} />
        <ProviderInfo>
          <ProviderName>{provider.name}</ProviderName>
          <ProviderMeta>
            <Icon name="calendar" size={14} color="#ff9000" />
            <ProviderMetaText>Segunda à Sexta</ProviderMetaText>
          </ProviderMeta>

          <ProviderMeta>
            <Icon name="clock" size={14} color="#ff9000" />
            <ProviderMetaText>8h às 18h</ProviderMetaText>
          </ProviderMeta>
        </ProviderInfo>
      </ProviderContainer>
    )}
  />
);
}

```

```typescript
import styled from 'styled-components/native';
import { RectButton } from 'react-native-gesture-handler';
import { Platform, TouchableOpacity, FlatList } from 'react-native';
import { getStatusBarHeight } from 'react-native-iphone-x-helper';
import { Provider } from './index';

export const Container = styled.View`
  flex: 1;
`;

export const Header = styled.View`
  padding: ${Platform.OS === 'ios' ? getStatusBarHeight() + 24 : 24}px 24px 24px;
  background: #28262e;

  flex-direction: row;
  justify-content: space-between;
  align-items: center;
`;

export const HeaderTitle = styled.Text`
  font-family: 'RobotoSlab-Regular';
  color: #f4ede8;
  font-size: 20px;
  line-height: 28px;
`;

export const UserName = styled.Text`
  font-family: 'RobotoSlab-Medium';
  color: #ff9000;
`;

export const ProfileButton = styled(TouchableOpacity)``;

export const UserAvatar = styled.Image`
  width: 56px;
  height: 56px;
  border-radius: 28px;
`;

export const ProvidersList = styled(FlatList as new () => FlatList<Provider>)`
  padding: 32px 24px 16px;
`;

export const ProviderContainer = styled(RectButton)`
  background: #3e3b47;
  border-radius: 10px;
  padding: 20px 16px;
  margin-bottom: 16px;
  flex-direction: row;
  align-items: center;
`;

export const ProviderAvatar = styled.Image`
  width: 72px;
  height: 72px;
  border-radius: 36px;
`;

export const ProviderName = styled.Text`
  font-family: 'RobotoSlab-Medium';
  font-size: 18px;
  line-height: 24px;
  color: #f4efe8;
`;

export const ProviderInfo = styled.View`
  margin-left: 20px;
  flex: 1;
`;

export const ProviderMeta = styled.View`
  flex-direction: row;
  align-items: center;
  margin-top: 8px;
`;

export const ProviderMetaText = styled.Text`
  font-family: 'RobotoSlab-Regular';
  color: #999591;
  margin-left: 12px;
`;

export const ProvidersListTitle = styled.Text`
  font-size: 25px;
  font-family: 'RobotoSlab-Medium';
  color: #f4ede8;
  margin-bottom: 24px;
`;

```

## Estrutura da criação de appointments
1. Fazer a estruturação do header e receber os route params.
```typescript
import React, { useCallback } from 'react';
import { useNavigation, useRoute } from '@react-navigation/native';

interface RouteParams {
  providerId: string;
}

const CreateAppointment: React.FC = () => {
  const { user } = useAuth();
  const { params } = useRoute();
  const { goBack } = useNavigation();
  const { providerId } = params as RouteParams;

  const navigateBack = useCallback(() => {
    goBack();
  }, [goBack]);

  return (
    <Container>
      <Header>
        <BackButton onPress={navigateBack}>
          <Icon name="arrow-left" size={24} color="#">
        </BackButton>
        <HeaderTitle>
          Agendamentos
        </HeaderTitle>
        <UserAvatar source={{ uri: user.avatar_url }}>
      </Header>
    </Container>
  )
};

```

2. Fazer a estilização do Header de acordo com o layout.

## Alternando entre providers
1. Fazer a criação da FlatList

```typescript
import React, { useCallback } from 'react';
import { useNavigation, useRoute } from '@react-navigation/native';

interface RouteParams {
  providerId: string;
}

const CreateAppointment: React.FC = () => {
  const { user } = useAuth();
  const { params } = useRoute();
  const { goBack } = useNavigation();
  const { providerId } = params as RouteParams;
  const [selectedProvider, setSelectedProvider] = useState(providerId);

  const navigateBack = useCallback(() => {
    goBack();
  }, [goBack]);

  const handleSelectedProvider = useCallback((id: string) => {
    setSelectedProvider(id);
  }, []);

  return (
    <Container>
      <Header>
        <BackButton onPress={navigateBack}>
          <Icon name="arrow-left" size={24} color="#">
        </BackButton>
        <HeaderTitle>
          Agendamentos
        </HeaderTitle>
        <UserAvatar source={{ uri: user.avatar_url }}>
      </Header>

      <ProvidersListContainer>
        <ProvidersList
          data={providers}
          keyExtractor={provider => provider.id}
          renderItem=(({ item: provider }) => (
            <ProviderContainer 
              onPress={() => handleSelectedProvider(provider.id)}
              selected={provider.id === selectedProvider}
            >
              <ProviderAvatar source={{ uri:provider.avatar_url }}/>
              <ProviderName>
                {provider.name}
              </ProviderName>
            </ProviderContainer>
          ))
        />
      </ProvidersListContainer>
    </Container>
  )
};

```

2. Fazer a estilização
```typescript
import styled from 'styled-components/native';

interface ProviderContainerProps {
  selected: boolean;
}

interface ProviderNameProps {
  selected: boolean;
}


export const ProvidersListContainer = styled.View`
  height: 112px;
`;

export const ProvidersList = styled(FlatList as new () => FlatList<Provider>)`
  padding: 32px 24px;
`;

export const ProviderContainer = styled(RectButton)<ProviderContainerProps>`
  flex-direction: row;
  align-items: center;
  margin-right: 20px;
  padding: 8px 12px;
  background: ${props => (props.selected ? '#ff9000' : '#3b3e47')};
  border-radius: 10px;
`;

export const ProviderAvatar = styled.Image`
  width: 32px;
  height: 32px;
  border-radius: 16px;
  margin-right: 8px;
`;

export const ProviderName = styled.Text<ProviderNameProps>`
  color: ${props => (props.selected ? '#232129' : '#f4ede8')};
  font-size: 16px;
  font-family: 'RobotoSlab-Medium';
`;

```
## Criando picker de data
1. Instalar a lib *@react-native-community/datetimepicker* e fazer o link.
```bash
$ yarn add @react-native-community/datetimepicker
```
2. Fazer a importação do DateTimePicker e passar uma prop *value={new Date()}*
```typescript

const [selectedDate, setSelectedDate] = useState(new Date());
const [showDatePicker, setShowDatePicker] = useState(false);

const handleToggleDatePicker = useCallback(() => {
  setShowDatePicker(state => !state);
}, []);

const handleChangeDate = useCallback((event: Event, date: Date | undefined) => {
  if (Platform.OS === 'android') {
    setShowDatePicker(false);
  }

  if (date) {
    setSelectedDate(false);
  }
}, []);

...

<Calendar>
  <Title>Escolha uma data</Title>
  <OpenDatePickerButton onPress={handleToggleDatePicker}>
    <OpenDatePickerButtonText>
      Selecionar outra data
    </OpenDatePickerButtonText>
  </OpenDatePickerButton>

{showDatePicker && (
  <DayTimePicker
    onChange={handleChangeDate}
    value={selectedDate}
    mode="date"
    display="calendar"
  />
)}

</Calendar>

```
3. Fazer estilização do OpenDatePickerButton, OpenDatePickerButtonText, Title
```typescript
export const Calendar = styled.View``;

export const Title = styled.Text`
  font-size: 25px;
  color: #f4ede8;
  font-family: 'RobotoSlab-Medium';
  margin: 0 24px 24px;
`;

export const OpenDatePickerButton = styled(RectButton)`
  height: 46px;
  background: #ff9000;
  border-radius: 10px;
  align-items: center;
  justify-content: center;
  margin: 0 24px;
`;

export const OpenDatePickerButtonText = styled.Text`
  font-family: 'RobotoSlab-Medium';
  font-size: 16px;
  color: #232129;
`;

```
## Buscando disponibilidade da API
1. Fazer chamada a api para verificar a disponibilidade do dia selecionado no calendário. Ou seja, sempre que houver uma alteração ou quando o provider selecinado seja alterado.

```typescript

interface ProviderDayAvailbilityItem {
  hour: number;
  available: boolean;
}

interface RouteParams {
  providerId: string;
}

const { params } = useRoute();
const routeParams = params as RouteParams;
const [selectedProvider, setSelectedProvider] = useState(routeParams.providerId);
const [selectedDate, setSelectedDate] = useState(new Date());

const [providerDayAvailability, setProviderDayAvailability] = useState<ProviderDayAvailbilityItem[]>([]);

useEffect(() => {
  api.get(`/providers/${selectedProvider}/day-availability`, {
    params: {
      year: selectedDate.getFullYear(),
      month: selectedDate.getMonth() + 1,
      day: selectedDate.getDate(),
    }
  }).then(response => setProviderDayAvailability(response.data));
}, [selectedDate, selectedProvider]);

```
## Debuggando app com Flipper
- Criada pelo facebook, permite fazer debug de aplicações nativas e híbridas.
- Consigo observar toda a árvore de elementos do app: páginas, estados, hooks e afins.
- Caso queira mostrar algum elemento específico, posso procurar direto na árvore mostrada no Flipper, ou posso ir lá na página de estilo do componente e seta um *displayName*. **Pois os nomes serão diferentes já que no final das contas serão nomes de componentes nativos.**

Exemplo
```typescript
export const Header = styled.View``;

Header.displayName = 'DashboardHeader';

```

1. Baixar o Flipper e Instalar.
2. Caso dê erro de OpenSSL quando abrir o Flipper, instalar OpenSSL, setar uma nova variável ambiente no windows chamada *OPENSSL_CONF* com o valor *C:\Program Files\OpenSSL-Win64\bin*
> Verificar se o valor é realmente o caminho que o *OpenSSL* foi instalado.
3. Editar a variável ambiente *Path* do windows e adicionar *C:\Program Files\OpenSSL-Win64\bin*.
4. Ir no windows e digitar *openssl* ou abrir o cmd e digitar *openssl*.

## Disponibilidade por turno
1. Instalar a lib *date-fns* para formatar a hora
2. Criar um calculo para exibir a disponibilidade do provider de acordo com o turno.
```typescript
const morningAvailability = useMemo(() => {
  return providerDayAvailability
  .filter(({ hour }) => hour < 12)
  .map(({ hour, available }) => ({
    hour,
    available,
    hourFormatted: format(new Date().setHour(hour), 'HH:00')
  }))
}, 
[providerDayAvailability]);

const afternoonAvailability = useMemo(() => {
  return providerDayAvailability
  .filter(({ hour }) => hour < 12)
  .map(({ hour, available }) => ({
    hour,
    available,
    hourFormatted: format(new Date().setHour(hour), 'HH:00')
  }))
}, 
[providerDayAvailability]);


return (
  ...
  {morningAvailability.map(({ hourFormatted }) => (
    <Title key={hourFormatted}>{hourFormatted}</Title>
  ))}

  {afternoonAvailability.map(({ hourFormatted }) => (
    <Title key={hourFormatted}>{hourFormatted}</Title>
  ))}
)
```
## Mostrando horário em tela
1. Fazer a estruturação e criar uma função, para quando o usuário acionar o *onPress* em algum horário, alterar o valor de uma variável de estado passando a hora. Então, esse botão vai ter uma prop *selected* que vai selecionar se a hora do agendamento é igual a hora da variável de estado.
```typescript
<Content>
  <ProvidersListContainer>
    <ProvidersList
      horizontal
      showsHorizontalScrollIndicator={false}
      data={providers}
      keyExtractor={provider => provider.id}
      renderItem={({ item: provider }) => (
        <ProviderContainer
          onPress={() => handleSelectProvider(provider.id)}
          selected={provider.id === selectedProvider}
        >
          <ProviderAvatar source={{ uri: provider.avatar_url }} />
          <ProviderName selected={provider.id === selectedProvider}>
            {provider.name}
          </ProviderName>
        </ProviderContainer>
      )}
    />
  </ProvidersListContainer>
  <Calendar>
    <Title>Escolha uma data</Title>
    <OpenDatePickerButton onPress={handleToggleDatePicker}>
      <OpenDatePickerButtonText>
        Selecionar outra data
      </OpenDatePickerButtonText>
    </OpenDatePickerButton>
    {showDatePicker && (
      <DayTimePicker
        onChange={handleChangeDate}
        value={selectedDate}
        mode="date"
        display="calendar"
      />
    )}
  </Calendar>

  <Schedule>
    <Title>Escolha o horário</Title>
    <Section>
      <SectionTitle>Manhã</SectionTitle>
      <SectionContent>
        {morningAvailability.map(({ hourFormatted, available, hour }) => (
          <Hour
            enabled={available}
            key={hourFormatted}
            selected={hour === selectedHour}
            onPress={() => handleSelectHour(hour)}
            available={available}
          >
            <HourText selected={hour === selectedHour}>
              {hourFormatted}
            </HourText>
          </Hour>
        ))}
      </SectionContent>
    </Section>

    <Section>
      <SectionTitle>Tarde</SectionTitle>
      <SectionContent>
        {afternoonAvailability.map(
          ({ hourFormatted, available, hour }) => (
            <Hour
              enabled={available}
              key={hourFormatted}
              selected={hour === selectedHour}
              onPress={() => handleSelectHour(hour)}
              available={available}
            >
              <HourText selected={hour === selectedHour}>
                {hourFormatted}
              </HourText>
            </Hour>
          ),
        )}
      </SectionContent>
    </Section>
  </Schedule>
</Content>
</Container>
```
2. É possível alterar as props de um componente pelo styled-componentes
```typescript
export const SectionContent = styled.ScrollView.attrs({
  contentContainerStyle: { paddingHorizontal: 24 },
  horizontal: true,
  showsHorizontalScrollIndicator: false,
})``;
```
## Criação do agendamento
1. Criar um botão que ao pressionado, irá fazer a chamada a api.
```typescript

const handleCreateAppointment = useCallback(() => {
  try {
    const date = new Date(selectedDate);
    date.setHours(selectedHour + 3); // +3 por causa do timezone
    date.setMinutes(0);
    date.setSeconds(0);
    date.setMilliseconds(0);

    await api.post('/appointments', {
      provide_id: selectedProvider,
      date,
    });

    navigate('AppointmentCreated', { date: date.getTime() }) // não posso mandar a class Date, por isso pegar apenas a data como number
  } catch (err) {
    Alert.alert(
      'Erro ao tentar criar agendamento!',
      'Ocorreu um erro ao tentar criar agendamento, tente novamente.'
    )
  }
}, [selectedProvider, selectedHour, selectedDate, navigate]);

<CreateAppointmentButton onPress={handleCreateAppointment}>
  <CreateAppointmentButtonText>
    Agendar
  </CreateAppointmentButtonText>
</CreateAppointmentButton>
```
## Criando estrutura da tela
1. Fazer a estrutura da página e estilizar.
```typescript
import React from 'react';
import Icon from 'react-native-vector-icons/Feather';
import {
  Container,
  Title,
  Description,
  OkButton,
  OkButtonText,
} from './styles';

const AppointmentCreated: React.FC = () => {

  return (
    <Container>
      <Icon name="check" size={80} color="#04D361" />
      <Title>
        Agendamento
        {'\n'}
        concluído
      </Title>
      <Description>{handleDateParams}</Description>
      <OkButton onPress={handleOkPressed}>
        <OkButtonText>OK</OkButtonText>
      </OkButton>
    </Container>
  );
};

export default AppointmentCreated;

```

2. Criar a navegação para quando o usuário clicar no bottão *ok*.
```typescript
import React, { useCallback } from 'react';
import { useNavigation } from '@react-navigation/native';

const AppointmentCreated = () => {

  const { reset } = useNavigation();

  const handleOkPressed = useCallback(() => {
    reset({
      routes: [{ name: 'Dashboard' }],
      index: 0;
    })
  }, [reset]);

  return(...);
}
```

> Dessa forma, as rotas do usuário serão resetadas e ele não conseguirá voltar para a página *AppointmentCreated*.

> Posso resetar a navegação do usuário para outra página, bastava adicionar ela no routes e informar o index dela. Assim, quando usuário utilizar o *voltar* do celular, ele voltará para a Dashboard.

## Formatando data
1. Utilizar o format do date-fns e o locale.
```typescript
import { format } from 'date-fns';
import ptBR from 'date-fns/locale';


interface RouteParams {
  date: number;
}

const { params } = useRoute();

const routeParams = params as RouteParams;

const formattedDate = useMemo(() => {
  return format(
    routeParams.date,
    "EEEE ', de' dd 'de' MMMM 'de' yyyy 'às' HH:mm 'h'",
    { locale: ptBR }
  )
}, [routeParams.date]);

return (
  ...
  <Description>
    {formattedDate}
  </Description>
);
```
## Criação da página de perfil
1. Fazer a estruturação e criação da página, lembrando sempre do ScrollView, para telas pequenas.

## Atualização do perfil
1. Fazer como foi feito na web, utilizar o Yup para fazer validação e enviar os dados pra api, pegar o retorno e atualizar o *AsyncStorage*.

```typescript
const handleUpdateProfile = useCallback(
    async (data: ProfileFormData) => {
      try {
        formRef.current?.setErrors({});
        const schema = Yup.object().shape({
          name: Yup.string().required('Nome obrigatório'),
          email: Yup.string()
            .required('Email obrigatório')
            .email('Digite um E-mail válido'),
          old_password: Yup.string(),
          password: Yup.string().when('old_password', {
            is: val => !!val.length,
            then: Yup.string().required('Campo obrigatório'),
            otherwise: Yup.string(),
          }),
          password_confirmation: Yup.string()
            .when('old_password', {
              is: val => !!val.length,
              then: Yup.string().required('Campo obrigatório'),
              otherwise: Yup.string(),
            })
            .oneOf([Yup.ref('password')], 'Confirmação obrigátoria'),
        });

        await schema.validate(data, { abortEarly: false });

        const {
          email,
          name,
          password,
          old_password,
          password_confirmation,
        } = data;

        const formData = {
          email,
          name,
          ...(old_password
            ? { password, old_password, password_confirmation }
            : {}),
        };

        const response = await api.put('/profile', formData);

        await updateUser(response.data);

        Alert.alert('Perfil alterado!');

        navigation.goBack();
      } catch (err) {
        if (err instanceof Yup.ValidationError) {
          const errors = getValidationErrors(err);

          formRef.current?.setErrors(errors);

          return;
        }

        Alert.alert(
          'Erro ao atualização do perfil',
          'Ocorreu um erro na alteração de perfil, tente novamente',
        );
      }
    },
    [navigation, updateUser],
  );
```
## Atualizando avatar com Image Picker
1. Instalar a lib *react-native-image-picker*
```bash
yarn add react-native-image-picker
```

2. Realizar o link(usuário android)
```bash
yarn react-native link react-native-image-picker
```

3. Ir em *android/app/src/main/AndroidManifest.xml* e adicionar
```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
4. Ir em android/gradle.properties e alterar a versão do flipper para 0.50.0

5. Executar *yarn android*
6. Fazer importação do *ImagePicker*, e utiliza-lo.
```typescript
import ImagePicker from 'react-native-image-picker';

const handleUpdateAvatar = useCallback(() => {
  ImagePicker.showImagePicker({
    title: 'Selecione uma foto',
    cancelButtonTitle: 'Cancelar',
    chooseFromLibraryButtonTitle: 'Selecionar da galeria',
    takePhotoButtonTitle: 'Usar câmera',
  }, response => {
    if (response.didCancel) {
      return;
    }

    if (response.error) {
      Alert.alert('Erro ao atualizar avatar');
      return;
    }

    const data = new FormData();

    data.append('avatar', {
      type: 'image/jpeg',
      name: `${user.id}.jpg`,
      uri: response.uri
    });

    api.patch('/users/avatar', data).then(resp => updateUser(resp.data));

  })
}, [user.id, updateUser]);
```
