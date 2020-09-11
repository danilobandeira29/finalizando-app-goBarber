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
